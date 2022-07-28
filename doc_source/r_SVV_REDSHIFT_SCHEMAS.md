# SVV\_REDSHIFT\_SCHEMAS<a name="r_SVV_REDSHIFT_SCHEMAS"></a>

Use SVV\_REDSHIFT\_SCHEMAS to view a list of all schemas that a user has access to\. This set of schemas includes the schemas on the cluster and the schemas from datashares provided by remote clusters\. 

## Table columns<a name="r_SVV_REDSHIFTL_SCHEMAS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_SCHEMAS.html)

## Sample query<a name="r_SVV_REDSHIFT_SCHEMAS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_SCHEMAS\.

```
SELECT *
FROM svv_redshift_schemas
WHERE database_name = 'tickit_db'
ORDER BY database_name,
    SCHEMA_NAME;

database_name |    schema_name     | schema_owner | schema_type | schema_acl | schema_option
--------------+--------------------+--------------+-------------+------------+---------------
   tickit_db  |       public       |       1      |    shared   |            |
```