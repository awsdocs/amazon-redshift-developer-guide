# SVV\_REDSHIFT\_TABLES<a name="r_SVV_REDSHIFT_TABLES"></a>

Use SVV\_REDSHIFT\_TABLES to view a list of all tables that a user has access to\. This set of tables includes the tables on the cluster and the tables from datashares provided by remote clusters\.

## Table columns<a name="r_SVV_REDSHIFT_TABLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_TABLES.html)

## Sample query<a name="r_SVV_REDSHIFT_TABLES-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_TABLES\.

```
SELECT *
FROM svv_redshift_tables
WHERE database_name = 'tickit_db' AND TABLE_NAME LIKE 'tickit_%'
ORDER BY database_name,
    TABLE_NAME;

database_name | schema_name |         table_name       | table_type | table_acl | remarks
--------------+-------------+--------------------------+------------+-----------+---------
   tickit_db  |    public   | tickit_category_redshift |    TABLE   |           |
   tickit_db  |    public   |   tickit_date_redshift   |    TABLE   |           |
   tickit_db  |    public   |   tickit_event_redshift  |    TABLE   |           |
   tickit_db  |    public   |  tickit_listing_redshift |    TABLE   |           |
   tickit_db  |    public   |   tickit_sales_redshift  |    TABLE   |           |
   tickit_db  |    public   |   tickit_users_redshift  |    TABLE   |           |
   tickit_db  |    public   |   tickit_venue_redshift  |    TABLE   |           |
```