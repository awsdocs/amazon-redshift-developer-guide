# SVV\_REDSHIFT\_SCHEMAS<a name="r_SVV_REDSHIFT_SCHEMAS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_REDSHIFT\_SCHEMAS to view a list of all schemas that a user has access to\. This includes the schemas on the cluster and the schemas from data shares provided by remote clusters\. 

## Table columns<a name="r_SVV_REDSHIFTL_SCHEMAS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_SCHEMAS.html)

## Sample query<a name="r_SVV_REDSHIFT_SCHEMAS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_SCHEMAS\.

```
SELECT *
FROM svv_redshift_schemas
WHERE database_name IN ('prod','test')
ORDER BY database_name,
         schema_name;
         
 database_name |    schema_name     | schema_owner | schema_type |        schema_acl        | schema_option
---------------+--------------------+--------------+-------------+--------------------------+---------------
 prod          | information_schema |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |
 prod          | pg_catalog         |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |
 prod          | public             |            1 | local       | rdsdb=UC/rdsdb~=UC/rdsdb |
 test          | information_schema |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |
 test          | pg_catalog         |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |
 test          | public             |            1 | local       | rdsdb=UC/rdsdb~=UC/rdsdb |
(6 rows)
```