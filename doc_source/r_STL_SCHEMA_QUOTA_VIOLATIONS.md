# STL\_SCHEMA\_QUOTA\_VIOLATIONS<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS"></a>

Records the occurrence, timestamp, XID, and other useful information when a schema quota is exceeded\.

Superusers can see all the records\. Schema owners can only see records related to the schemas they own\.

## Table columns<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SCHEMA_QUOTA_VIOLATIONS.html)

## Sample queries<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS-sample-queries"></a>

The following query shows the result of quota violation:

```
SELECT userid, TRIM(SCHEMA_NAME) "schema_name", quota, disk_usage, disk_usage_pct, timestamp FROM
stl_schema_quota_violations WHERE SCHEMA_NAME = 'sales_schema' ORDER BY timestamp DESC;
```

This query returns the following sample output for the specified schema:

```
userid | schema_name  | quota | disk_usage | disk_usage_pct |timestamp
-------+--------------+-------+------------+----------------+----------------------------
104    | sales_schema | 2048  | 2798       |  136.62        | 2020-04-20 20:09:25.494723
(1 row)
```