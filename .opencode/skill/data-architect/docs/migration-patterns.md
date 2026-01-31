# Data Migration Patterns

**Document ID:** `data-architect/docs/migration-patterns`  
**Purpose:** Patterns and strategies for data migrations in the browser

---

## 1. IndexedDB Versioning

### How It Works

```typescript
const DB_VERSION = 3; // Increment to trigger migration

const request = indexedDB.open('nexus_db', DB_VERSION);

request.onupgradeneeded = (event) => {
  const db = event.target.result;
  const oldVersion = event.oldVersion;
  const newVersion = event.newVersion;
  
  // Execute migrations sequentially
  if (oldVersion < 1) migration_v0_v1(db);
  if (oldVersion < 2) migration_v1_v2(db);
  if (oldVersion < 3) migration_v2_v3(db);
};
```

---

## 2. Schema Evolution Patterns

### 2.1 Add Field (Non-Breaking)

```typescript
async function addField(store: IDBObjectStore, field: string, defaultValue: any) {
  const cursor = await store.openCursor();
  
  while (cursor) {
    const record = cursor.value;
    if (!(field in record)) {
      record[field] = defaultValue;
      await cursor.update(record);
    }
    await cursor.continue();
  }
}
```

### 2.2 Rename Field (Breaking)

```typescript
async function renameField(
  store: IDBObjectStore, 
  oldName: string, 
  newName: string
) {
  const cursor = await store.openCursor();
  
  while (cursor) {
    const record = cursor.value;
    if (oldName in record) {
      record[newName] = record[oldName];
      delete record[oldName];
      await cursor.update(record);
    }
    await cursor.continue();
  }
}
```

### 2.3 Create New Index

```typescript
function migration_v1_v2(db: IDBDatabase, transaction: IDBTransaction) {
  const store = transaction.objectStore('files');
  
  if (!store.indexNames.contains('byMimeType')) {
    store.createIndex('byMimeType', 'mimeType', { unique: false });
  }
}
```

---

## 3. Safe Migration Strategies

### 3.1 Parallel Schema Pattern

For large changes, maintain both versions:

```typescript
// Phase 1: Dual-write
async function saveDocument(doc: Document) {
  await saveToV1(doc);  // Old schema
  await saveToV2(doc);  // New schema
}

// Phase 2: Background migration
async function migrateInBackground() {
  const v1Docs = await getAllV1();
  for (const doc of v1Docs) {
    if (!await existsInV2(doc.id)) {
      await saveToV2(transformV1toV2(doc));
    }
  }
}

// Phase 3: Switch reads
async function getDocument(id: string) {
  return await getFromV2(id);
}

// Phase 4: Cleanup
async function cleanup() {
  await deleteV1Store();
}
```

### 3.2 Lazy Migration Pattern

```typescript
async function getDocument(id: string): Promise<DocumentV2> {
  const doc = await db.get('documents', id);
  
  if (doc.schemaVersion < 2) {
    const migrated = migrateToV2(doc);
    await db.put('documents', migrated);
    return migrated;
  }
  
  return doc;
}
```

### 3.3 Transactional Migration

```typescript
async function atomicMigration() {
  const tx = db.transaction(['source', 'target'], 'readwrite');
  
  try {
    const source = tx.objectStore('source');
    const target = tx.objectStore('target');
    
    const all = await source.getAll();
    for (const item of all) {
      await target.put(transform(item));
    }
    
    await tx.done; // Commit
  } catch (error) {
    tx.abort(); // Automatic rollback
    throw error;
  }
}
```

---

## 4. Rollback Procedures

### 4.1 Backup Before Migration

```typescript
async function backupStore(storeName: string): Promise<any[]> {
  const tx = db.transaction(storeName, 'readonly');
  const store = tx.objectStore(storeName);
  const backup = await store.getAll();
  
  await db.put('_backups', {
    id: `${storeName}_${Date.now()}`,
    data: backup,
    timestamp: Date.now(),
  });
  
  return backup;
}
```

### 4.2 Restore from Backup

```typescript
async function restoreStore(storeName: string, backupId: string) {
  const backup = await db.get('_backups', backupId);
  
  const tx = db.transaction(storeName, 'readwrite');
  const store = tx.objectStore(storeName);
  
  await store.clear();
  for (const item of backup.data) {
    await store.put(item);
  }
  
  await tx.done;
}
```

---

## Common Pitfalls

1. **Not testing migrations with production data** - Always test with real data
2. **Forgetting to increment version** - IndexedDB won't run upgrade without new version
3. **Long migrations blocking UI** - Use Web Workers for heavy migrations
4. **Not backing up** - ALWAYS backup before destructive migrations
5. **Assuming clean data** - Validate each record before transforming
