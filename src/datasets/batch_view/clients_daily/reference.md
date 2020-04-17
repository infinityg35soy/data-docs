# Clients Daily

<!-- toc -->

# Introduction

{{#include ./intro.md}}

# Data Reference

## Example Queries

#### Compute Churn for a one-day cohort:

```sql
SELECT
  submission_date,
  approx_count_distinct(client_id) AS cohort_dau
FROM
  telemetry.clients_daily
WHERE
  submission_date > '2017-08-31'
  AND submission_date < '2017-10-01'
  AND profile_creation_date LIKE '2017-09-01%'
GROUP BY 1
ORDER BY 1
```

#### Distribution of pings per client per day:

```sql
SELECT
  normalized_channel,
  CASE
    WHEN pings_aggregated_by_this_row > 50 THEN 50
    ELSE pings_aggregated_by_this_row
  END AS pings_per_day,
  approx_count_distinct(client_id) AS client_count
FROM telemetry.clients_daily
WHERE
  submission_date = '2017-09-01'
  AND normalized_channel <> 'Other'
GROUP BY
  normalized_channel,
  pings_per_day
ORDER BY
  pings_per_day,
  normalized_channel
```

## Scheduling

This dataset is updated daily via the
[telemetry-airflow](https://github.com/mozilla/telemetry-airflow) infrastructure.
The job runs as part of the [`main_summary` DAG](https://github.com/mozilla/telemetry-airflow/blob/master/dags/main_summary.py).

## Schema

The data is partitioned by `submission_date`.

As of 2019-11-28, the current version of the `clients_daily` dataset is `v6`.

# Code Reference

This dataset is generated by
[`bigquery-etl`](https://github.com/mozilla/bigquery-etl/blob/25b702d0824b96ec1342d653296adfbe1302027d/sql/telemetry_derived/clients_daily_v6/query.sql).
Refer to this repository for information on how to run or augment the dataset.