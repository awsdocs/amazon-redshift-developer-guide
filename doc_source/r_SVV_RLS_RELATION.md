# SVV\_RLS\_RELATION<a name="r_SVV_RLS_RELATION"></a>

Use SVV\_RLS\_RELATION to view a list of all relations that are RLS\-protected\.

Only users with the sys:secadmin role can query this view\.

## Table columns<a name="r_SVV_RLS_RELATION-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_RLS_RELATION.html)

## Sample query<a name="r_SVV_RLS_RELATION-sample-query"></a>

The following example displays the result of the SVV\_RLS\_RELATION\.

```
ALTER TABLE tickit_category_redshift ROW LEVEL SECURITY ON FOR DATASHARES;       

            
--Inspect RLS state on the relations using SVV_RLS_RELATION.
SELECT datname, relschema, relname, relkind, is_rls_on, is_rls_datashare_on FROM svv_rls_relation ORDER BY relname;

  datname  | relschema |        relname           | relkind | is_rls_on | is_rls_datashare_on
-----------+-----------+--------------------------+---------+-----------+---------------------
 tickit_db |   public  | tickit_category_redshift |  table  |      t    |     t
(1 row)
```