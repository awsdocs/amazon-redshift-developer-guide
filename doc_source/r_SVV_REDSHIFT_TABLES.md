# SVV\_REDSHIFT\_TABLES<a name="r_SVV_REDSHIFT_TABLES"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_REDSHIFT\_TABLES to view a list of all tables that a user has access to\. This includes the tables on the cluster and the tables from data shares provided by remote clusters\.

## Table columns<a name="r_SVV_REDSHIFT_TABLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_TABLES.html)

## Sample query<a name="r_SVV_REDSHIFT_TABLES-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_TABLES\.

```
SELECT *
FROM svv_redshift_tablesWHERE database_name 
IN ('prod','test')
AND table_name 
LIKE 'sales'ORDER BY database_name, schema_name;

 database_name | schema_name | table_name | table_type | table_acl | remarks
---------------+-------------+------------+------------+-----------+---------
 prod          | public      | sales      | TABLE      |           |
 test          | public      | sales      | TABLE      |           |
```