# SVV\_ALL\_TABLES<a name="r_SVV_ALL_TABLES"></a>

Use SVV\_ALL\_TABLES to view a union of Amazon Redshift tables as shown in SVV\_REDSHIFT\_TABLES and the consolidated list of all external tables from all external schemas\. For information about Amazon Redshift tables, see [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)\.

SVV\_ALL\_TABLES is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_ALL_TABLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_TABLES.html)

## Sample queries<a name="r_SVV_ALL_TABLES-sample-queries"></a>

The following example returns the output of SVV\_ALL\_TABLES\.

```
SELECT *
FROM svv_all_tables
WHERE database_name = 'tickit_db'
ORDER BY TABLE_NAME,
    SCHEMA_NAME
LIMIT 5;

 database_name | schema_name |        table_name        | table_type | table_acl | remarks
---------------+-------------+--------------------------+------------+-----------+---------
   tickit_db   |    public   | tickit_category_redshift |    TABLE   |           |
   tickit_db   |    public   |   tickit_date_redshift   |    TABLE   |           |
   tickit_db   |    public   |   tickit_event_redshift  |    TABLE   |           |
   tickit_db   |    public   | tickit_listing_redshift  |    TABLE   |           |
   tickit_db   |    public   |   tickit_sales_redshift  |    TABLE   |           |
```