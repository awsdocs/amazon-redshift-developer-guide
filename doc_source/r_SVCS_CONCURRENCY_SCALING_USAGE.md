# SVCS\_CONCURRENCY\_SCALING\_USAGE<a name="r_SVCS_CONCURRENCY_SCALING_USAGE"></a>

Records the usage periods for concurrency scaling\. Each usage period is a consecutive duration where an individual concurrency scaling cluster is actively processing queries\.

By default, this view is visible by a superuser only\. The database superuser can choose to open it up to all users\. 

## Table columns<a name="r_SVCS_CONCURRENCY_SCALING_USAGE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVCS_CONCURRENCY_SCALING_USAGE.html)

## Sample queries<a name="r_SVCS_CONCURRENCY_SCALING_USAGE-sample-queries"></a>

To view the usage duration in seconds for a specific period, enter the following query: 

```
select * from svcs_concurrency_scaling_usage order by start_time;

start_time | end_time | queries | usage_in_seconds
----------------------------+----------------------------+---------+------------------
2019-02-14 18:43:53.01063 | 2019-02-14 19:16:49.781649 | 48 | 1977
```