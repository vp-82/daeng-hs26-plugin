---
name: gtfs-conventions
description: Rules for querying the GTFS feed in this course repo. Use whenever the task involves raw/*.txt, trips, stop_times, calendar, routes, stops, counting trips, deriving a service day, or parsing GTFS times.
---

# GTFS conventions for this repo

Apply these before writing any query against `raw/`.

## 1. Counting trips

`stop_times` holds one row per stop of a journey, not one row per journey. A trip
with 12 stops contributes 12 rows.

- Never answer "how many trips" with `COUNT(*)` over a join that includes `stop_times`.
- Count trips from `trips`, or use `COUNT(DISTINCT trip_id)`.
- State the grain of the result before writing the query.

## 2. The service day is declared, not derived

There is no date column in `trips` or `stop_times`. Do not invent one from a time.

The operating dates come from `calendar.txt`: weekday flags plus `start_date` and
`end_date`. Expand that window into one row per `service_id` and date, then join to
`trips` on `service_id`.

`calendar_dates.txt` holds dated exceptions and overrides `calendar.txt` where both
apply. This slice has no `calendar_dates.txt`.

Never assume every trip runs every day. Some services are weekday-only or
weekend-only, and their routes are absent from other days entirely.

## 3. Times can exceed 24 hours

A journey continuing past midnight is published as `24:17:00`, not `00:17:00`, so
that it stays on the service day it started. Consequences:

- `departure_time` and `arrival_time` are text, not `TIME`. Casting will fail.
- To get elapsed minutes, split on `:` and compute, do not cast.
- A time of `24:17:00` belongs to the previous service day. It is not an error.

## 4. Column types in this slice

DuckDB infers `start_date` and `end_date` as `BIGINT`. Cast to `VARCHAR` before
`strptime`. Calendar weekday flags are integers, so compare to `1`, not `'1'`.

## 5. Before returning any aggregate

State in one sentence what one row of the result means. If you cannot, the query is
not finished.
