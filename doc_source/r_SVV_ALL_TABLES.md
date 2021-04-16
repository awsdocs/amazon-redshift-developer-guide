# SVV\_ALL\_TABLES<a name="r_SVV_ALL_TABLES"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_ALL\_TABLES to view a union of Amazon Redshift tables as shown in SVV\_REDSHIFT\_TABLES and the consolidated list of all external tables from all external schemas\. For information about Amazon Redshift tables, see [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)\.

SVV\_ALL\_TABLES is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_ALL_TABLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_TABLES.html)

## Sample queries<a name="r_SVV_ALL_TABLES-sample-queries"></a>

The following example returns the output of SVV\_ALL\_TABLES\.

```
SELECT *
FROM svv_all_tables
WHERE database_name IN ('prod','test')
AND   table_name LIKE 'sales'
ORDER BY database_name,
         schema_name;
 
 database_name | schema_name | table_name |   table_type   | table_acl | remarks
---------------+-------------+------------+----------------+-----------+---------
 prod          | public      | sales      | TABLE          |           |
 test          | public      | sales      | TABLE          |           |
 test          | spectrum    | sales      | EXTERNAL TABLE |           |
```