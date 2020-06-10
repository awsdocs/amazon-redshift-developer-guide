# SVV\_EXTERNAL\_SCHEMAS<a name="r_SVV_EXTERNAL_SCHEMAS"></a>

Use SVV\_EXTERNAL\_SCHEMAS to view information about external schemas\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

SVV\_EXTERNAL\_SCHEMAS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_EXTERNAL_SCHEMAS-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_SCHEMAS.html)

## Example<a name="r_SVV_EXTERNAL_SCHEMAS-example"></a>

The following example shows details for external schemas\. 

```
select * from svv_external_schemas;

esoid  | eskind | schemaname | esowner | databasename | esoptions                                                   
-------+--------+------------+---------+--------------+-------------------------------------------------------------
100133 |      1 | spectrum   |     100 | redshift     | {"IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
```