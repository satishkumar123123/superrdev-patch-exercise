# Engineering Notes

### 1. SQL Filter Precedence
- What: Missing parentheses around OR condition leaked archived tasks and bypassed status filters.
- Found: Reviewed SQL native queries in TaskRepository and DB scripts.
- Changed: Enclosed title and description LIKE conditions in parentheses: `AND (LOWER(title) LIKE :term OR LOWER(description) LIKE :term)`.
- Why: Preserves `archived = FALSE` and status filtering integrity.

### 2. Search Race Condition & UX
- What: Rapid keystrokes caused out-of-order API responses to overwrite current search state; failed fetches left loading stuck.
- Found: UI review of React search hooks lacking request cancellation.
- Changed: Added `AbortController` in useEffect cleanup, cleared previous errors on new fetch, and wrapped execution in try-catch-finally.
- Why: Guarantees UI displays only the latest query result and handles errors gracefully.

### 3. Pagination & Filter Synchronization
- What: Filtering or typing while on page > 0 retained the stale page offset, producing empty screens.
- Found: Filter state updates did not trigger page resets.
- Changed: Reset page index to 0 whenever search terms or status filters change.
- Why: Ensures users always land on the first page of filtered subsets.

### 4. Artificial Delay & Input Validation
- What: Hardcoded `Thread.sleep()` artificially delayed search requests; invalid parameters threw 500 errors.
- Found: Traced query complexity logic in TaskController.
- Changed: Removed Thread.sleep; added explicit 400 Bad Request responses for negative pages and invalid TaskStatus enums.
- Why: Drops artificial latency to 0ms and provides deterministic HTTP error contracts.
