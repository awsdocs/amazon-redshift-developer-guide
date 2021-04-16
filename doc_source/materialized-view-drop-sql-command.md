# DROP MATERIALIZED VIEW<a name="materialized-view-drop-sql-command"></a>

Removes a materialized view\.

For more information about materialized views, see [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\.

## Syntax<a name="mv_DROP_MATERIALIZED_VIEW-synopsis"></a>

```
DROP MATERIALIZED VIEW [ IF EXISTS ]  mv_name
```

## Parameters<a name="mv_DROP_MATERIALIZED_VIEW-parameters"></a>

IF EXISTS  
A clause that specifies to check if the named materialized view exists\. If the materialized view doesn't exist, then the `DROP MATERIALIZED VIEW` command returns an error message\. This clause is useful when scripting, to keep the script from failing if you drop a nonexistent materialized view\.

*mv\_name*  
The name of the materialized view to be dropped\.

## Usage Notes<a name="mv_DROP_MATERIALIZED_VIEW-usage"></a>

Only the owner of a materialized view can use `DROP MATERIALIZED VIEW` on that view\.

## Example<a name="mv_DROP_MATERIALIZED_VIEW-examples"></a>

The following example drops the `tickets_mv` materialized view\.

```
DROP MATERIALIZED VIEW tickets_mv;
```