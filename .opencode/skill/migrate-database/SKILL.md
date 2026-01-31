---
name: migrate-database
description: "Schema analysis, migration creation, and data integrity validation for database systems. Guides the complete process of modifying database schemas including impact analysis, migration generation, and validation. Use when: (1) Adding new tables or modifying existing schemas, (2) Adding indexes, constraints, or foreign keys, (3) Performing data migrations or schema refactoring, (4) Planning breaking changes with dual-write patterns."
license: MIT
---

# Skill: Migrate Database

> Schema analysis, migration creation, and data integrity validation for database systems.

---

## Approaches

### Quick (10 min)
For simple column additions:
1. Add column to schema with default
2. Generate migration
3. Push and test

### Complete (20-45 min)
Full migration process:
1. Current State Analysis (PLANNING)
2. Schema Definition (EXECUTION)
3. Migration Generation (EXECUTION)
4. Data Migration if needed (EXECUTION)
5. Testing (VERIFICATION)

### Incremental (multiple sessions)
For breaking changes:
1. Session 1: Analysis + Plan
2. Session 2: New schema parallel
3. Session 3: Dual-write implementation
4. Session 4: Migration + Cutover
5. Session 5: Cleanup

---

## Main Prompt

```
Perform a **database schema analysis and migration** using Drizzle ORM.

## Target
[Specify the change, e.g., "Add notifications table" or "Add index to users.email"]

## Change Type
[Select: NEW_TABLE | ALTER_TABLE | ADD_INDEX | ADD_CONSTRAINT | DATA_MIGRATION]

---

## Phase 1: Current State Analysis (PLANNING Mode)
1.  **Review Existing Schema**: Examine `src/db/schema/*.ts` or equivalent
2.  **Identify Relationships**: Map foreign keys and dependencies
3.  **Check for Conflicts**: Ensure no naming conflicts
4.  **Document**:
    -   Current schema state
    -   Proposed changes
    -   Affected queries/services

---

## Phase 2: Schema Definition (EXECUTION Mode)
1.  **Create/Modify Schema File** in `src/db/schema/`
    -   [ ] Define table with Drizzle syntax
    -   [ ] Add constraints (primaryKey, notNull, unique)
    -   [ ] Define foreign keys with references()
    -   [ ] Add indexes for queried columns
2.  **Export from Index**: Update `src/db/schema/index.ts`
3.  **Define Relations** if applicable

---

## Phase 3: Migration Generation (EXECUTION Mode)
1.  **Generate Migration**:
    ```bash
    pnpm drizzle-kit generate:pg --name [migration_name]
    ```
2.  **Review Generated SQL**
3.  **Verify**:
    -   [ ] Column types correct
    -   [ ] Constraints defined
    -   [ ] No destructive changes
    -   [ ] Default values sensible

---

## Phase 4: Data Migration (if needed)
1.  **Create Data Migration Script** for data transformation
2.  **Backfill Strategy**: Populate new columns for existing rows
3.  **Rollback Plan**: Document reversal procedure

---

## Phase 5: Testing (VERIFICATION Mode)
1.  **Run Migration Locally**:
    ```bash
    pnpm drizzle-kit push:pg
    ```
2.  **Test Affected Queries**
3.  **Add/Update Repository Tests**
4.  **Build Verification**:
    ```bash
    pnpm build
    pnpm test
    ```

---

## Expected Deliverables
1.  Updated schema in `src/db/schema/`
2.  Generated migration in `drizzle/`
3.  Updated exports
4.  Tests pass
5.  Rollback documentation

---

## Constraints
-   **Never DROP columns** without explicit approval
-   **Add NOT NULL with DEFAULT** for new columns on existing tables
-   **Test on copy** before production
-   **Use UUIDs** for primary keys
-   **Add indexes** for WHERE clause columns
```

---

## Schema Definition Patterns

### Table Definition
```typescript
import { pgTable, uuid, text, timestamp, boolean } from 'drizzle-orm/pg-core';

export const notifications = pgTable('notifications', {
  id: uuid('id').defaultRandom().primaryKey(),
  userId: uuid('user_id').notNull().references(() => users.id),
  title: text('title').notNull(),
  message: text('message').notNull(),
  read: boolean('read').default(false).notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
});
```

### Index Definition
```typescript
import { index } from 'drizzle-orm/pg-core';

export const notificationsUserIdx = index('notifications_user_idx')
  .on(notifications.userId);

export const notificationsCreatedIdx = index('notifications_created_idx')
  .on(notifications.createdAt);
```

### Relations
```typescript
import { relations } from 'drizzle-orm';

export const notificationsRelations = relations(notifications, ({ one }) => ({
  user: one(users, {
    fields: [notifications.userId],
    references: [users.id],
  }),
}));
```

---

## Migration Safety Rules

| Change Type | Risk | Mitigation |
|-------------|------|------------|
| Add column (nullable) | Low | None needed |
| Add column (NOT NULL) | Medium | Add DEFAULT value |
| Drop column | High | Backup first, verify no usage |
| Rename column | High | Use dual-write pattern |
| Add index | Low | May lock table briefly |
| Drop index | Medium | Verify query plans |
| Add foreign key | Medium | Validate existing data |

---

## Rollback Procedures

### Before Migration
1. Backup affected tables
2. Document current state
3. Write rollback script

### Rollback Script Template
```sql
-- Rollback: [migration_name]
-- Date: [date]
-- Reason: [if needed]

-- Undo changes
ALTER TABLE notifications DROP COLUMN IF EXISTS new_column;

-- Verify
SELECT COUNT(*) FROM notifications;
```

---

## Skill Chaining

### Can Be Called By
- **create-api**: When new entity is needed
- **data-architect**: When planning schema

### Chains To
- **create-api**: After migration for API implementation
  - Trigger: New table created
  - Input: Table schema

- **data-architect**: For complex schema decisions
  - Trigger: Breaking change requires analysis
  - Input: Schema requirements

---

## Evolution

### v1.1.0 (2026-01-07)
- Adapted to Anthropic skill standard
- Simplified frontmatter (name, description, license)
- Added comprehensive triggers in description
- Removed deprecated metadata fields

### v1.0.0 (2026-01-06)
- Migrated from .skills/ to .opencode/skill/
- Added approaches section
- Added skill chaining
- Enhanced with safety rules and patterns
