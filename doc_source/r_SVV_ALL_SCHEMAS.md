# SVV\_ALL\_SCHEMAS<a name="r_SVV_ALL_SCHEMAS"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Use SVV\_ALL\_SCHEMAS to view a union of Amazon Redshift schemas as shown in SVV\_REDSHIFT\_SCHEMAS and the consolidated list of all external schemas from all databases\. For more information about Amazon Redshift schemas, see [SVV\_REDSHIFT\_SCHEMAS](r_SVV_REDSHIFT_SCHEMAS.md)\.

SVV\_ALL\_SCHEMAS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\.

## Table columns<a name="r_SVV_ALL_SCHEMAS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ALL_SCHEMAS.html)

## Sample query<a name="r_SVV_ALL_SCHEMAS-sample-query"></a>

The following example returns the output of SVV\_ALL\_SCHEMAS\.

```
SELECT *
FROM svv_all_schemas
WHERE database_name IN ('prod','test')
ORDER BY database_name,
         schema_name;

 database_name |    schema_name     | schema_owner | schema_type |        schema_acl        | source_database |                                   schema_option
---------------+--------------------+--------------+-------------+--------------------------+-----------------+-----------------------------------------------------------------------------------
 prod          | information_schema |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |                 |
 prod          | pg_catalog         |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |                 |
 prod          | public             |            1 | local       | rdsdb=UC/rdsdb~=UC/rdsdb |                 |
 test          | information_schema |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |                 |
 test          | pg_catalog         |            1 | local       | rdsdb=UC/rdsdb~=U/rdsdb  |                 |
 test          | public             |            1 | local       | rdsdb=UC/rdsdb~=UC/rdsdb |                 |
 test          | spectrum           |          100 | external    |                          | sampledb        | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/MySpectrumRole"}
(7 rows)
```