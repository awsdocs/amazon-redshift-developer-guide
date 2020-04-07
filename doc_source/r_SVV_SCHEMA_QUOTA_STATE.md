# SVV\_SCHEMA\_QUOTA\_STATE<a name="r_SVV_SCHEMA_QUOTA_STATE"></a>

Displays the quota and the current disk usage for each schema\.

Regular users can see information for schemas for which they have USAGE permission\. Superusers can see information for all schemas in the current database\.

SVV\_SCHEMA\_QUOTA\_STATE is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_SVV_SCHEMA_QUOTA_STATE-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_SCHEMA_QUOTA_STATE.html)

## Sample Query<a name="r_SVV_SCHEMA_QUOTA_STATE-sample-query"></a>

The following example displays the quota and the current disk usage for the schema\.

```
SELECT TRIM(SCHEMA_NAME) "schema_name", QUOTA, disk_usage, disk_usage_pct 
FROM svv_schema_quota_state 
WHERE SCHEMA_NAME = 'dp18419_schema';

schema_name | quota | disk_usage | disk_usage_pct
----------------+---------------------------+--------------------
dp18419_schema | 2048 | 20 | 0.9800000000000001
```