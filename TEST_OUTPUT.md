# Part 2 Test Output

## Before Fixes

**Command:** `npx vitest run src/tests/part2.test.ts`  
**Tests:** 3 | **Passed:** 1 | **Failed:** 2  

```
 RUN  v2.1.8

 ❯ src/tests/part2.test.ts (3) 4641ms
   ❯ task access control (3) 1938ms
     × a viewer cannot update a task 681ms
     × a viewer cannot create a task 599ms
     ✓ a member can create a task 655ms

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 2 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot update a task
AssertionError: expected 200 to be 403 // Object.is equality

- Expected  403
+ Received  200

 ❯ src/tests/part2.test.ts:57:24

 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot create a task
AssertionError: expected 403 to be 401 // Object.is equality

- Expected  401
+ Received  403

 ❯ src/tests/part2.test.ts:70:24

 Test Files  1 failed (1)
      Tests  2 failed | 1 passed (3)
   Duration  7.47s
```

### Failures Diagnosed

| Test | Failure Reason | Type |
|---|---|---|
| **Test A** — a viewer cannot update a task | `PATCH /api/tasks/[id]` has no role/membership check — viewer receives `200` instead of `403` | Code bug |
| **Test B** — a viewer cannot create a task | Assertion used `toBe(401)` but API correctly returns `403 Forbidden` (viewer is authenticated, just not permitted) | Wrong assertion in test |

---

## After Fixes

**Fixes applied:**
- **Test B assertion** corrected from `toBe(401)` → `toBe(403)` in `part2.test.ts`
- **Test D added** — `a member can update a task` with corrected endpoint `PATCH /api/tasks/:taskId`

**Command:** `npx vitest run src/tests/part2.test.ts`  
**Tests:** 4 | **Passed:** 3 | **Failed:** 1  

```
 RUN  v2.1.8

 ❯ src/tests/part2.test.ts (4) 4637ms
   ❯ task access control (4) 2365ms
     × a viewer cannot update a task 630ms
     ✓ a viewer cannot create a task 593ms
     ✓ a member can create a task 577ms
     ✓ a member can update a task 563ms

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/tests/part2.test.ts > task access control > a viewer cannot update a task
AssertionError: expected 200 to be 403 // Object.is equality

- Expected  403
+ Received  200

 ❯ src/tests/part2.test.ts:57:24

 Test Files  1 failed (1)
      Tests  1 failed | 3 passed (4)
   Duration  7.19s
```

### Remaining Failure

| Test | Status | Root Cause |
|---|---|---|
| **Test A** — a viewer cannot update a task | ❌ Still failing | `PATCH /api/tasks/[id]` route in `src/app/api/tasks/[id]/route.ts` does not perform a membership/role check. The handler only verifies the user is authenticated, allowing any authenticated user — including viewers — to update tasks. The fix requires looking up the task's `projectId`, fetching the user's membership, and returning `403` if `canEditTasks(role)` is false. |
| **Test B** — a viewer cannot create a task | ✅ Fixed | Assertion corrected from `toBe(401)` to `toBe(403)` |
| **Test C** — a member can create a task | ✅ Pass | No change needed |
| **Test D** — a member can update a task | ✅ Pass | Endpoint corrected to `PATCH /api/tasks/:taskId` |
