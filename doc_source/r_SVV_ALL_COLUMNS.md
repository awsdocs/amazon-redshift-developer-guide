# SVV\_ALL\_COLUMNS<a name="r_SVV_ALL_COLUMNS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_ALL\_COLUMNS to view a union of columns from Amazon Redshift tables as shown in SVV\_REDSHIFT\_COLUMNS and the consolidated list of all external columns from all external tables\. For information about Amazon Redshift columns, see [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)\.

SVV\_ALL\_COLUMNS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="r_SVV_ALL_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_COLUMNS.html)

## Sample queries<a name="r_SVV_ALL_COLUMNS-sample-queries"></a>

The following example returns the output of SVV\_ALL\_COLUMNS\.

```
SELECT *
FROM svv_all_columns
WHERE database_name IN ('prod','test')
AND   table_name LIKE 'sales'
ORDER BY database_name,
         schema_name;

 database_name | schema_name | table_name | column_name | ordinal_position |       column_default        | is_nullable |          data_type          | character_maximum_length | numeric_precision | numeric_scale | remarks
---------------+-------------+------------+-------------+------------------+-----------------------------+-------------+-----------------------------+--------------------------+-------------------+---------------+---------
 prod          | public      | sales      | salesid     |                1 | integer                     | NO          | integer                     |                          |                32 |             0 |
 prod          | public      | sales      | listid      |                2 | integer                     | NO          | integer                     |                          |                32 |             0 |
 prod          | public      | sales      | saletime    |               10 | timestamp without time zone | YES         | timestamp without time zone |                          |                   |               |
 test          | public      | sales      | salesid     |                1 |                             | NO          | integer                     |                          |                32 |             0 |
 test          | public      | sales      | listid      |                2 |                             | NO          | integer                     |                          |                32 |             0 |
 test          | public      | sales      | sellerid    |                3 |                             | NO          | integer                     |                          |                32 |             0 |
 test          | spectrum    | sales      | salesid     |                1 |                             | YES         | integer                     |                          |                32 |             0 |
 test          | spectrum    | sales      | listid      |                2 |                             | YES         | integer                     |                          |                32 |             0 |
```