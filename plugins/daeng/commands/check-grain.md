---
description: State the grain of a query result and verify it against the data before accepting the answer.
---

Check the grain of the query or result under discussion.

1. State in one sentence what one row of the result means. Name the columns that
   uniquely identify a row.
2. Name every table in the query and state what one row of that table means.
3. Identify any join that can multiply rows. For each one, say whether the right-hand
   side is unique on the join key.
4. Run the verification: compare the aggregate against an independent count computed a
   different way. For trip counts that means `COUNT(DISTINCT trip_id)` from `trips`
   alone, without `stop_times`.
5. Report both numbers. If they differ, do not present the result. Explain the
   mechanism that produced the difference.

Do not rewrite the query until steps 1 to 5 are reported.
