# Permissioned Search, High Selectivity (Score Sort)

- **Join**: Single Feature (BM25 Score)
- **Description**: A Full Text Search (BM25) drives the ranking, restricted by a high-selectivity (narrow) foreign filter on `users` (`u.about_me ||| 'security'`). On the 20M dataset, the foreign filter matches 4,577 users (`0.20%`, below both `paradedb.rank_join_min_rows = 10,000` and `paradedb.hash_join_inlist_pushdown_max_distinct_values = 20,000`): `RankJoinRule` declines `RankJoinExec` and rewrites the join into `HashJoinExec(fetch=10)` over a `lazy_sort` probe scan using `BitsetFromPostings` dynamic `InList` pushdown.

## Query Info (statistics from 20M dataset):

- `stackoverflow_posts.title ||| 'how using get create'`: ~2.03M matches (~10.1%)
- `users.about_me ||| 'security'`: 4,577 / 2,261,291 users (~0.20%), yielding 6,506 joined post matches
- Under database convention, this narrow filter represents high selectivity (filters out ~99.8% of `users`).

## Native PostgreSQL Baseline Omission

See `../join_permissioned_search_low_selectivity/README.md` for why `postgres.sql` is omitted across the `join_permissioned_search_*` tiers.
