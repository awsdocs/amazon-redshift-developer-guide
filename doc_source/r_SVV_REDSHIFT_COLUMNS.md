# SVV\_REDSHIFT\_COLUMNS<a name="r_SVV_REDSHIFT_COLUMNS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_REDSHIFT\_COLUMNS to view a list of all columns that a user has access to\. This includes the columns on the cluster and the columns from data shares provided by remote clusters\.

SVV\_REDSHIFT\_COLUMNS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_REDSHIFT_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_COLUMNS.html)

## Sample query<a name="r_SVV_REDSHIFT_COLUMNS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_COLUMNS\.

```
SELECT *
FROM svv_redshift_columns
WHERE database_name IN ('prod','test')
AND   table_name LIKE 'sales'
ORDER BY database_name,
         schema_name;

 database_name | schema_name | table_name | column_name | ordinal_position |          data_type          |       column_default        | is_nullable | encoding | distkey | sortkey | column_acl | remarks
---------------+-------------+------------+-------------+------------------+-----------------------------+-----------------------------+-------------+----------+---------+---------+------------+---------
 prod          | public      | sales      | salesid     |                1 | integer                     | integer                     | NO          | az64     | f       |       0 |            |
 prod          | public      | sales      | listid      |                2 | integer                     | integer                     | NO          | none     | t       |       1 |            |
 prod          | public      | sales      | sellerid    |                3 | integer                     | integer                     | NO          | none     | f       |       2 |            |
 test          | public      | sales      | salesid     |                1 | integer                     |                             | NO          | az64     | f       |       0 |            |
 test          | public      | sales      | listid      |                2 | integer                     |                             | NO          | none     | t       |       1 |            |
 test          | public      | sales      | sellerid    |                3 | integer                     |                             | NO          | none     | f       |       2 |            |
```