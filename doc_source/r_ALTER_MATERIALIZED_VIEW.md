# ALTER MATERIALIZED VIEW<a name="r_ALTER_MATERIALIZED_VIEW"></a>

Enables automatic refreshing of a materialized view\. 

## Syntax<a name="r_ALTER_MATERIALIZED_VIEW-synopsis"></a>

```
ALTER MATERIALIZED VIEW mv_name
[ AUTO REFRESH { YES | NO } ]
[ ROW LEVEL SECURITY { ON | OFF } [FOR DATASHARES] ];
```

## Parameters<a name="r_ALTER_MATERIALIZED_VIEW-parameters"></a>

AUTO REFRESH \{ YES \| NO \}  
A clause that turns on or off automatic refreshing of a materialized view\. For more information about automatic refresh of materialized views, see [Refreshing a materialized view](materialized-view-refresh.md)\.

ROW LEVEL SECURITY \{ ON \| OFF \}  
A clause that turns on or off row\-level security for a materialized view\. When row\-level security is turned on, you can read only the rows that the row\-level policy permits for access\. When there isn't any policy granting a user access to the table or view, the user can't see any records\. For more information, see [CREATE RLS POLICY](r_CREATE_RLS_POLICY.md)\.

FOR DATASHARES  
A clause that determines whether a materialized view is RLS\-protected over datashares\. By default, a RLS\-protected materialized view is also RLS\-protected over datashares\.

## Examples<a name="r_ALTER_MATERIALIZED_VIEW-examples"></a>

The following example enables the `tickets_mv` materialized view to be automatically refreshed\.

```
ALTER MATERIALIZED VIEW tickets_mv AUTO REFRESH YES
```