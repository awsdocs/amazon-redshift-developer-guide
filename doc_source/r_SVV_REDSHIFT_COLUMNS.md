# SVV\_REDSHIFT\_COLUMNS<a name="r_SVV_REDSHIFT_COLUMNS"></a>

Use SVV\_REDSHIFT\_COLUMNS to view a list of all columns that a user has access to\. This set of columns includes the columns on the cluster and the columns from datashares provided by remote clusters\.

SVV\_REDSHIFT\_COLUMNS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_REDSHIFT_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_COLUMNS.html)

## Sample query<a name="r_SVV_REDSHIFT_COLUMNS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_COLUMNS\.

```
SELECT *
FROM svv_redshift_columns
WHERE database_name = 'tickit_db'
    AND TABLE_NAME = 'tickit_sales_redshift'
ORDER BY COLUMN_NAME,
    TABLE_NAME,
    database_name
LIMIT 5;

database_name | schema_name |       table_name      | column_name | ordinal_position | data_type | column_default | is_nullable | encoding | distkey | sortkey | column_acl  | remarks
--------------+-------------+-----------------------+-------------+------------------+-----------+----------------+-------------+----------+---------+---------+-------------+--------
   tickit_db  |   public    | tickit_sales_redshift |   buyerid   |        4         |  integer  |                |      NO     |   az64   |  False  |    0    |             |
   tickit_db  |   public    | tickit_sales_redshift |  commission |        9         |  numeric  |      (8,2)     |     YES     |   az64   |  False  |    0    |             |
   tickit_db  |   public    | tickit_sales_redshift |    dateid   |        6         |  smallint |                |      NO     |   none   |  False  |    1    |             |
   tickit_db  |   public    | tickit_sales_redshift |   eventid   |        5         |  integer  |                |      NO     |   az64   |  False  |    0    |	      |
   tickit_db  |   public    | tickit_sales_redshift |   listid    |        2         |  integer  |                |      NO     |   az64   |  True   |    0    |             |
```