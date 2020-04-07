# STL\_SCHEMA\_QUOTA\_VIOLATIONS<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS"></a>

Records the occurrence, timestamp, XID, and other useful information when a schema quota is exceeded\.

Superusers can see all the records\. Schema owners can only see records related to the schemas they own\.

## Table Columns<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_SCHEMA_QUOTA_VIOLATIONS.html)

## Sample Queries<a name="r_STL_SCHEMA_QUOTA_VIOLATIONS-sample-queries"></a>

The following query shows the result of quota violation:

```
SELECT TRIM(SCHEMA_NAME) "schema_name", QUOTA, disk_usage, disk_usage_pct  
FROM stl_schema_quota_violations 
WHERE SCHEMA_NAME = 'dp18419_schema';
```

This query returns the following sample output for the specified schema:

```
  schema_name   | quota | disk_usage | disk_usage_pct
----------------+-------+------------+----------------
 dp18419_schema |    17 |         30 |         176.47
```