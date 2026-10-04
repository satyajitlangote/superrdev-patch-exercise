# NOTES.md

## Summary of changes (6 fixes, one commit each)

- **F1 – SQL precedence:** added parentheses in `TaskRepository.java`, mirrored in `db/queries/search_tasks.sql` and both Oracle queries. `AND` binds before `OR`, so archived rows leaked and the status filter was ignored for title matches.
- **F2 – validation:** invalid `status`, `page < 1`, `pageSize < 1` now return HTTP 400 (was 500); `pageSize` capped at 100.
- **F6 – latency:** removed an artificial `Thread.sleep((10 - len) * 100)` that blocked a request thread; empty-query time went from 1034 ms to 104 ms.
- **F3 – race:** `useTasks.js` ignores responses from superseded requests (ignore flag), so stale data cannot overwrite fresh results.
- **F4 – pagination:** changing search text or status resets to page 1.
- **F5 – error handling:** `setError(null)` when a request starts; `setLoading(false)` in `finally`.

## What I chose not to change (and why)

- In-memory pagination: correct at this data size; DB-level paging would risk a rewrite.
- `System.out.println` logging and unescaped `LIKE` wildcards: minor, no proven user-facing bug.
- Structure, startup commands and dependencies: untouched, per the exercise rules.

## Biggest remaining risk

Search scans every row and `total` is computed in Java with no indexes, so performance degrades as task counts grow into the thousands.

## Tools/AI used

I used OpenCode with the MiMo model to review the code and suggest candidate bugs, and I verified each change myself: every bug was reproduced with curl against the running app before fixing, and the same tests were re-run after each fix with before/after numbers recorded. Backend fixes are test-verified; the three frontend fixes are compile-verified (`npm run build`) plus code review, but not browser-click-tested (no browser connection available).
