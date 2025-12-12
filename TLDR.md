# Quotient Quickstart

> Natural language tests executed by AI agents.

## 1. Write a test

Create `tests/auth.qt.md`:

```markdown
# Feature: Authentication

## Test: Login redirect
Unauthenticated users accessing /dashboard should be redirected to /login with a message.

## Test: Session restore
Returning users within 24h should remain logged in.
```

## 2. Run with an agent

```
Run quotient tests in tests/*.qt.md against http://localhost:3000.
Report PASS/FAIL for each with observations.
```

## 3. Get results

```
✓ PASS  Login redirect
✓ PASS  Session restore
TOTAL: 2 passed, 0 failed
```

---

**Test anatomy:** Context → Action → Expectation

**File convention:** `*.qt.md`

**Key principle:** Specify outcomes, not implementation details.

[Full documentation →](README.md)
