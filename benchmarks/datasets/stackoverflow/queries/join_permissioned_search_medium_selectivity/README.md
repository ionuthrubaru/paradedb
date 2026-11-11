# Permissioned Search, Medium Selectivity (Score Sort)

- **Join**: Single Feature (BM25 Score)
- **Description**: A Full Text Search (BM25) drives the ranking, restricted by a medium-selectivity foreign filter on `users` (`u.about_me ||| 'software'`). On the 20M dataset, the foreign filter sits below `UNSCORED_SELECTIVITY_THRESHOLD` (`3.01% < 8%`) with 68,087 distinct `users.id`s inside `(20,000, 500,000]`: `RankJoinRule` declines `RankJoinExec` and rewrites the join into `HashJoinExec(fetch=10)` over a `lazy_sort` probe scan with `lazy_sort` `InList` pushdown (`paradedb.lazy_sort_inlist_pushdown_max_distinct_values`).

## Query Info (statistics from 20M dataset):

- `stackoverflow_posts.title ||| 'how using get create'`: ~2.03M matches (~10.1%)
- `users.about_me ||| 'software'`: 68,087 / 2,261,291 users (~3.01%), yielding 112,342 joined post matches

## Native PostgreSQL Baseline Omission

See `../join_permissioned_search_low_selectivity/README.md` for why `postgres.sql` is omitted across the `join_permissioned_search_*` tiers.
