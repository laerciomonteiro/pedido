# AGENTS.md

Project: Stitch (HTML/Tailwind CSS + OpenCode Plugin Configuration)

---

## Project Overview

Minimal HTML/CSS/JavaScript project using Tailwind CSS via CDN with OpenCode plugin configuration.

**Key Files:**
- `code.html` - Main HTML file (581 lines) with Tailwind CSS styling
- `assets/` - Static assets directory (contains img/ subdirectory)
- `.opencode/` - OpenCode plugin configuration with skills and agent specs

---

## Build/Lint/Test Commands

**No build system configured.** This project uses CDN-loaded Tailwind CSS without a build step.

**No linting configured.** There is no ESLint, Prettier, or other linting setup.

**No testing configured.** There are no test files or testing framework setup.

---

## Code Style Guidelines

### HTML/CSS/JavaScript Conventions

**Structure:**
- HTML uses Tailwind CSS via CDN with class-based styling
- Custom CSS defined in `<style>` blocks for specific effects (e.g., `.polaroid` animations)
- Dark mode support via `class="dark"` on `<html>` element
- Google Fonts: Be Vietnam Pro (display), Material Symbols Outlined (icons)

**Tailwind Configuration:**
- Custom colors: `#ee2b5b` (primary), `#f8f6f6` (background-light), `#181113` (background-dark)
- Custom border radius values: default `0.5rem`, lg `1rem`, xl `1.5rem`, full `9999px`
- Dark mode: class-based (not media query)

**Naming Conventions:**
- CSS classes: kebab-case (`.polaroid`, custom Tailwind extensions)
- IDs: descriptive, snake_case or kebab-case
- HTML attributes: lowercase (`class`, `id`, `href`, `src`)

**JavaScript:**
- Vanilla JavaScript, no frameworks
- Use `<script>` tags with descriptive comments
- Functions: camelCase naming
- Constants: UPPER_SNAKE_CASE

**Code Organization:**
- Keep related CSS rules grouped
- Use whitespace for readability
- Comment complex animations or interactions

**Error Handling:**
- No error handling required for static HTML
- Future JS additions should use try/catch for critical operations

---

## OpenCode Plugin Configuration

This project includes an OpenCode plugin configuration at `.opencode/`.

**Structure:**
- `.opencode/skill/` - Skills for agent operations (20+ skills including code-review, perf-optimizer, create-api, etc.)
- `.opencode/command/` - Command definitions (analyze, decompose, review, rfc)
- `.opencode/specs/` - Architecture and workflow documentation
- `.opencode/agent/` - Agent configuration files

**Key Skills:**
- `opencode-specs` - Master engine specifications
- `sop-source-citation` - Mandatory source citation protocol (SOP-001)
- `sop-inline-audit` - Standardized inline tagging (SOP-002) for bugs/vulnerabilities (@BUG, @VULN, @SECURITY, @TECH-DEBT, @TODO, @FIXME, @PERF)
- `code-review` - Systematic code review methodology
- `codebase-analysis` - Codebase analysis patterns
- `perf-optimizer` - Vue 3/Nuxt 4 performance optimization

**Important Protocols:**

**SOP-001 (Source Citation):** All claims must cite sources using format `<!-- @REF(path/to/file.ts:42): Description -->`

**SOP-002 (Inline Audit):** Use standardized tags in code comments:
- `@BUG` - Critical logic errors
- `@VULN` - Security vulnerabilities
- `@SECURITY` - Security concerns or hardcoded secrets
- `@TECH-DEBT` - Suboptimal code or architectural debt
- `@TODO` - Pending features
- `@FIXME` - Critical tasks to resolve
- `@PERF` - Performance bottlenecks

---

## Adding New Code

When modifying `code.html`:
1. Use Tailwind utility classes first (check CDN documentation)
2. Add custom CSS in `<style>` block only for Tailwind cannot handle
3. Test dark mode by toggling the `dark` class on `<html>`
4. Ensure responsive design with mobile-first approach
5. Use semantic HTML elements

When creating new HTML files:
1. Follow the same structure as `code.html`
2. Include Tailwind CDN script
3. Use the same color scheme and fonts
4. Apply dark mode support
5. Add custom styles in `<style>` block only when necessary

---

## Asset Management

- Images and static files go in `assets/` directory
- Use relative paths: `./assets/img/filename.jpg`
- Optimize images for web (WebP format recommended)

---

## Important Notes

- This is a minimal project without a build process
- No package manager (npm, yarn, pnpm) in use
- Direct file edits expected - no hot reload available
- Use browser developer tools for debugging
