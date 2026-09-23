# SPEC — Issue #1: Task counter shows total tasks instead of active tasks remaining

## Problem

`getActiveCount(tasks)` in `src/taskManager.js` returns `tasks.length` — the total
number of tasks — instead of the number of incomplete tasks. The footer counter
rendered in `src/ui.js` (`render()`, the `activeCount` line) therefore reads
"3 items left" even after a task has been checked off, making it useless.

Source: `src/taskManager.js:70-72`

```js
function getActiveCount(tasks) {
  return tasks.length;   // BUG
}
```

## Expected behaviour

`getActiveCount` returns the count of tasks whose `completed` flag is `false`.
With three tasks and one completed, the footer reads "2 items left".

## Existing test coverage (red test already present)

Confirmed by running `npm test` before any change: **2 of 21 tests fail**, both in
the `getActiveCount` suite. No new test needs to be written; both go red → green.

- `tests/taskManager.test.js:145` — "returns only the count of incomplete tasks when
  some are completed": expected `2`, received `3`.
- `tests/taskManager.test.js:135` — "returns 0 when all tasks are completed":
  expected `0`, received `2`.

The two sibling tests already pass and must keep passing:
- `getActiveCount([])` → 0
- two tasks, none completed → 2

## Plan

1. Run `npm install && npm test` to confirm the red test fails for the stated reason.
2. Change `getActiveCount` to filter on `!task.completed` and return that length.
   Match the file's existing style: ES5 `function` expressions, `var`, no arrow
   functions — the module is loaded directly as a browser global as well as by Jest.
3. Update the JSDoc above it to drop the `BUG:` note.
4. Re-run `npm test` — all tests green.
5. No change needed in `src/ui.js`: it already calls `getActiveCount` and handles
   singular/plural. Scope is the counter only; issues #2 and #3 are out of scope.

## Delivery

- Branch `fix/issue-1-active-task-count` off `main`.
- Commit referencing the issue, PR opened against `main` with `Closes #1`.
