# Framework-Specific Self-Review Checklists

Apply only the section matching your project's technology stack. These extend the universal checklist in Phase 4.8.

---

## Qt / C++

**Signals & Slots:**
- Every `connect()` — are signal/slot signatures compatible? Is the receiver alive when the signal fires?
- Dynamically created widgets — are they properly parented or explicitly deleted?

**Event Handling:**
- `eventFilter()` — does it consume events it shouldn't? Does it interfere with other filters installed on the same object?
- Are events being blocked that downstream widgets need?

**Collections & Widgets:**
- `QTreeWidget`/`QListWidget` — are items properly cleared before repopulating? Could stale items remain from a previous load?
- Layout — does `FlowLayout`/`QHBoxLayout` handle empty items, single items, and overflow correctly?

**Build System:**
- MOC — `Q_OBJECT` classes need MOC. If multiple test classes share one binary, use manual `main()` with per-class `QTest::qExec()` and fresh argv copies.
- Runtime DLLs — copy shared libraries (`.dll`, `.so`) to the test output directory before running tests.

---

## JavaScript / TypeScript / React

- `useEffect` — are dependencies correct? Could stale closures cause stale state?
- Event handlers — are they properly cleaned up on unmount? Memory leaks from listeners?
- Async state updates — `setState` after unmount? Race conditions on rapid user input?
- Prop drilling / context — are all required props passed? Undefined props at render time?

---

## Python / Django / Flask

- ORM queries — N+1 problems? Missing `select_related`/`prefetch_related`?
- View functions — is authentication/authorization checked? Missing `@login_required`?
- Template rendering — are all context variables passed? Missing keys cause silent `KeyError` or empty output.
- Database migrations — are they included? Do they handle existing data correctly?

---

## Go

- Goroutine safety — shared state protected by mutex? Channel closed before read?
- Error handling — every error return checked? Nil error ≠ no error.
- Context cancellation — long-running operations respect `ctx.Done()`?
