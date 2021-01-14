# ALTER MATERIALIZED VIEW<a name="r_ALTER_MATERIALIZED_VIEW"></a>

Enables automatic refreshing of a materialized view\. 

## Syntax<a name="r_ALTER_MATERIALIZED_VIEW-synopsis"></a>

```
ALTER MATERIALIZED VIEW mv_name AUTO REFRESH [ YES | NO ]
```

## Parameters<a name="r_ALTER_MATERIALIZED_VIEW-parameters"></a>

 *mv\_name*   
The name of the materialized view to alter\.

AUTO REFRESH \[ YES \| NO \]  
A clause that enables or disables automatic refreshing of a materialized view\. For more information about automatic refresh of materialized views, see [Refreshing a materialized view](materialized-view-refresh.md)\.

## Examples<a name="r_ALTER_MATERIALIZED_VIEW-examples"></a>

The following example enables the `tickets_mv` materialized view to be automatically refreshed\.

```
ALTER MATERIALIZED VIEW tickets_mv AUTO REFRESH YES
```