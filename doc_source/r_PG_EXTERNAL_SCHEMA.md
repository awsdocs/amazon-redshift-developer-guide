# PG\_EXTERNAL\_SCHEMA<a name="r_PG_EXTERNAL_SCHEMA"></a>

Stores information about external schemas\.

PG\_EXTERNAL\_SCHEMA is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\. 

## Table columns<a name="r_PG_EXTERNAL_SCHEMA-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_EXTERNAL_SCHEMA.html)

## Example<a name="r_PG_EXTERNAL_SCHEMA-example"></a>

The following example shows details for external schemas\. 

```
select esoid, nspname as schemaname, nspowner, esdbname as external_db, esoptions 
from pg_namespace a,pg_external_schema b where a.oid=b.esoid;

esoid  | schemaname      | nspowner | external_db | esoptions                                                   
-------+-----------------+----------+-------------+-------------------------------------------------------------
100134 | spectrum_schema |      100 | spectrum_db | {"IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
100135 | spectrum        |      100 | spectrumdb  | {"IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
100149 | external        |      100 | external_db | {"IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
```