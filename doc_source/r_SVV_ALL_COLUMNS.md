# SVV\_ALL\_COLUMNS<a name="r_SVV_ALL_COLUMNS"></a>

Use SVV\_ALL\_COLUMNS to view a union of columns from Amazon Redshift tables as shown in SVV\_REDSHIFT\_COLUMNS and the consolidated list of all external columns from all external tables\. For information about Amazon Redshift columns, see [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)\.

SVV\_ALL\_COLUMNS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="r_SVV_ALL_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_COLUMNS.html)

## Sample queries<a name="r_SVV_ALL_COLUMNS-sample-queries"></a>

The following example returns the output of SVV\_ALL\_COLUMNS\.

```
SELECT *
FROM svv_all_columns
WHERE database_name = 'tickit_db'
    AND TABLE_NAME = 'tickit_sales_redshift'
ORDER BY COLUMN_NAME,
    SCHEMA_NAME
LIMIT 5;

 database_name | schema_name |     table_name        | column_name | ordinal_position | column_default | is_nullable | data_type | character_maximum_length | numeric_precision | numeric_scale | remarks
 --------------+-------------+-----------------------+-------------+------------------+----------------+-------------+-----------+--------------------------+-------------------+---------------+---------
   tickit_db   |    public   | tickit_sales_redshift |    buyerid  |        4         |                |      NO     |  integer  |                          |         32        |       0       |
   tickit_db   |    public   | tickit_sales_redshift | commission  |        9         |                |     YES     |  numeric  |                          |          8        |	2       |
   tickit_db   |    public   | tickit_sales_redshift |    dateid   |        7         |                |      NO     |  smallint |                          |         16        |       0       |
   tickit_db   |    public   | tickit_sales_redshift |   eventid   |        5         |                |      NO     |  integer  |                          |         32        |       0       |
   tickit_db   |    public   | tickit_sales_redshift |    listid   |        2         |                |      NO     |  integer  |                          |         32        |       0       |
```