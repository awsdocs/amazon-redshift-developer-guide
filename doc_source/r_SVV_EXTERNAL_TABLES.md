# SVV\_EXTERNAL\_TABLES<a name="r_SVV_EXTERNAL_TABLES"></a>

Use SVV\_EXTERNAL\_TABLES to view details for external tables\. For more information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

SVV\_EXTERNAL\_TABLES is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

## Table columns<a name="r_SVV_EXTERNAL_TABLES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_EXTERNAL_TABLES.html)

## Example<a name="r_SVV_EXTERNAL_TABLES-example"></a>

The following example shows details svv\_external\_tables with a predicate on the external schema used by a federated query\.

```
select schemaname, tablename from svv_external_tables where schemaname = 'apg_tpch';
schemaname  | tablename
------------+-----------
apg_tpch    | customer
apg_tpch    | lineitem
apg_tpch    | nation
apg_tpch    | orders
apg_tpch    | part
apg_tpch    | partsupp
apg_tpch    | region
apg_tpch    | supplier
(8 rows)
```