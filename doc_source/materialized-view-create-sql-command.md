# CREATE MATERIALIZED VIEW<a name="materialized-view-create-sql-command"></a>

Creates a materialized view\. 

## Syntax<a name="mv_CREATE_MATERIALIZED_VIEW-synopsis"></a>

```
CREATE MATERIALIZED VIEW mv_name
[ BACKUP { YES | NO } ]
[ table_attributes ]   
AS query
```

## Parameters<a name="mv_CREATE_MATERIALIZED_VIEW-parameters"></a>

BACKUP  
A clause that specifies whether the materialized view is included in automated and manual cluster snapshots, which are stored in Amazon S3\.  
The default value for `BACKUP` is `YES`\.  
You can specify `BACKUP NO` to save processing time when creating snapshots and restoring from snapshots, and to reduce the amount of storage required in Amazon S3\.  
The `BACKUP NO` setting has no effect on automatic replication of data to other nodes within the cluster, so tables with `BACKUP NO` specified are restored in a node failure\.

 *table\_attributes*   
A clause that specifies how the data in the materialized view is distributed, including the following:  
+  The distribution style for the materialized view, in the format `DISTSTYLE { EVEN | ALL | KEY }`\. If you omit this clause, the distribution style is `AUTO`\. For more information, see [Distribution styles](c_choosing_dist_sort.md)\.
+ The distribution key for the materialized view, in the format `DISTKEY ( distkey_identifier )`\. For more information, see [Designating distribution styles](t_designating_distribution_styles.md)\.
+ The sort key for the materialized view, in the format `SORTKEY ( column_name [, ...] )`\. For more information, see [Choosing sort keys](t_Sorting_data.md)\.

AS *query*  
A valid `SELECT` statement which defines the materialized view and its content\. The result set from the query defines the columns and rows of the materialized view\. For information about limitations when creating materialized views, see [Limitations](#mv_CREATE_MATERIALIZED_VIEW-limitations)\.  
Furthermore, specific SQL language constructs used in the query determines whether the materialized view can be incrementally or fully refresehed\. For information about the refresh method, see [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)\. For information about the limitations for incremental refresh, see [Limitations for incremental refresh](materialized-view-refresh-sql-command.md#mv_REFRESH_MARTERIALIZED_VIEW_limitations)\.  
If the query contains an SQL command that doesn't support incremental refresh, Amazon Redshift displays a message indicating that the materialized view will use a full refresh\. The message may or may not be displayed depending on the SQL client application\. For example, psql displays the message, and a JDBC client may not\. Check the `state` column of the [STV\_MV\_INFO](r_STV_MV_INFO.md) to see the refresh type used by a materialized view\.

## Usage notes<a name="mv_CREATE_MARTERIALIZED_VIEW_usage"></a>

To create a materialized view, you must have the following privileges:
+ CREATE privileges for a schema\.
+ Table\-level SELECT privilege on the base tables to create a materialized view\. Even if you have column\-level privileges on specific columns, you can't create a materialized view on only those columns\. 

## DDL updates to materialized views or base tables<a name="materialized-view-ddl"></a>

When using materialized views in Amazon Redshift, follow these usage notes for data definition language \(DDL\) updates to materialized views or base tables\.
+ You can add columns to a base table without affecting any materialized views that reference the base table\.
+ Some operations can leave the materialized view in a state that can't be refreshed at all\. Examples are operations such as renaming or dropping a column, changing the type of a column, and changing the name of a schema\. Such materialized views can be queried but can't be refreshed\. In this case, you must drop and recreate the materialized view\. 
+ In general, you can't alter a materialized view's definition \(its SQL statement\)\.
+ You can't rename a materialized view\. 

## Limitations<a name="mv_CREATE_MATERIALIZED_VIEW-limitations"></a>

You can't define a materialized view that references or includes any of the following:
+ Any other materialized view, a standard view,  an external table, or system tables and views\.
+ Temporary tables\.
+ User\-defined functions\.
+ The ORDER BY, LIMIT, or OFFSET clause\.
+ Late binding references to base tables\. In other words, any base tables or related columns referenced in the defining SQL query of the materialized view must exist and must be valid\. 

## Example<a name="mv_CREATE_MARTERIALIZED_VIEW_examples"></a>

The following example creates a materialized view from three base tables which are joined and aggregated\. Each row represents a category with the number of tickets sold\. When you query the tickets\_mv materialized view, you directly access the precomputed data in the tickets\_mv materialized view\.

```
CREATE MATERIALIZED VIEW tickets_mv AS
    select   catgroup,
    sum(qtysold) as sold
    from     category c, event e, sales s
    where    c.catid = e.catid
    and      e.eventid = s.eventid
    group by catgroup;
```

The following example creates a materialized view similar to the previous example and uses the aggregate function MAX\(\) that is currently not supported for incremental refresh\. You can verify that by querying the STV\_MV\_INFO table and see that the ‘state' column is 0\.

```
CREATE MATERIALIZED VIEW tickets_mv_max AS
    select   catgroup,
    max(qtysold) as sold
    from     category c, event e, sales s
    where    c.catid = e.catid
    and      e.eventid = s.eventid
    group by catgroup;
    
SELECT name, state FROM STV_MV_INFO
     name        | state
-----------------+--------
 tickets_mv      | 1
 tickets_mv_max  | 0
```

For details about materialized view overview and SQL commands used to refresh and drop materialized views, see the following topics:
+ [Creating materialized views in Amazon Redshift](materialized-view-overview.md)
+ [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)
+ [DROP MATERIALIZED VIEW](materialized-view-drop-sql-command.md)