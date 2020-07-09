# CREATE MATERIALIZED VIEW<a name="materialized-view-create-sql-command"></a>

Creates a materialized view based on one or more Amazon Redshift tables or external tables that you can create using Spectrum or federated query\. For information about Spectrum, see [Querying external data using Amazon Redshift Spectrum](c-using-spectrum.md)\. For information about federated query, see [Querying data with federated queries in Amazon Redshift](federated-overview.md)\.

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
+  The distribution style for the materialized view, in the format `DISTSTYLE { EVEN | ALL | KEY }`\. If you omit this clause, the distribution style is `EVEN`\. For more information, see [Distribution styles](c_choosing_dist_sort.md)\.
+ The distribution key for the materialized view, in the format `DISTKEY ( distkey_identifier )`\. For more information, see [Designating distribution styles](t_designating_distribution_styles.md)\.
+ The sort key for the materialized view, in the format `SORTKEY ( column_name [, ...] )`\. For more information, see [Choosing sort keys](t_Sorting_data.md)\.

AS *query*  
A valid `SELECT` statement which defines the materialized view and its content\. The result set from the query defines the columns and rows of the materialized view\. For information about limitations when creating materialized views, see [Limitations](#mv_CREATE_MATERIALIZED_VIEW-limitations)\.  
Furthermore, specific SQL language constructs used in the query determines whether the materialized view can be incrementally or fully refreshed\. For information about the refresh method, see [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)\. For information about the limitations for incremental refresh, see [Limitations for incremental refresh](materialized-view-refresh-sql-command.md#mv_REFRESH_MARTERIALIZED_VIEW_limitations)\.  
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
+ Any other materialized view, a standard view, or system tables and views\.
+ Temporary tables\.
+ User\-defined functions\.
+ The ORDER BY, LIMIT, or OFFSET clause\.
+ Late binding references to base tables\. In other words, any base tables or related columns referenced in the defining SQL query of the materialized view must exist and must be valid\. 
+ System administration functions\. For a list, see [System administration functions](r_System_administration_functions.md)\. 
+ System information functions\. For a list, see [System information functions](r_System_information_functions.md)\. 
+ Leader node\-only functions: CURRENT\_SCHEMA, CURRENT\_SCHEMAS, HAS\_DATABASE\_PRIVILEGE, HAS\_SCHEMA\_PRIVILEGE, HAS\_TABLE\_PRIVILEGE, AGE, CURRENT\_TIME, CURRENT\_TIMESTAMP, LOCALTIME, NOW\.
+ Date functions: CURRENT\_DATE, DATE, DATE\_PART, DATE\_TRUNC, DATE\_CMP\_TIMESTAMPTZ, SYSDATE, TIMEOFDAY, TO\_TIMESTAMP\. When defining a materialized view, consider the following functions with specific input argument types: DATE is immutable for timestamp, DATE\_PART is immutable for date, time, interval, and time\-tz, DATE\_TRUNC is immutable for the following data type: date, timestamp, and interval\. You must use functions that are immutable in order to successfully create materialized views\. Otherwise, Amazon Redshift blocks the creation of materialized views that contain functions that are not immutable\. For more information about functions, see [Function volatility categories](https://www.postgresql.org/docs/8.0/xfunc-volatility.html)\.
+ Math functions: RANDOM\.
+ Date type formatting functions: TO\_CHAR WITH TIMESTAMPTZ\. 

## Examples<a name="mv_CREATE_MARTERIALIZED_VIEW_examples"></a>

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

The following example uses a UNION ALL clause to join the Amazon Redshift `public_sales` table and the Redshift Spectrum `spectrum.sales` table to create a material view `mv_sales_vw`\. For information about the CREATE EXTERNAL TABLE command for Amazon Redshift Spectrum, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\. The Redshift Spectrum external table references the data on Amazon S3\.

```
CREATE MATERIALIZED VIEW mv_sales_vw as
select salesid, qtysold, pricepaid, commission, saletime from public.sales
union all
select salesid, qtysold, pricepaid, commission, saletime from spectrum.sales
```

The following example creates a materialized view `mv_fq` based on a federated query external table\. For information about federated query, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

```
CREATE MATERIALIZED VIEW mv_fq as select firstname, lastname from apg.mv_fq_example;
            
select firstname, lastname from mv_fq;
 firstname | lastname
-----------+----------
 John      | Day
 Jane      | Doe
(2 rows)
```

The following example shows the definition of a materialized view\.

```
SELECT pg_catalog.pg_get_viewdef('mv_sales_vw'::regclass::oid, true);
    
pg_get_viewdef
---------------------------------------------------
create materialized view mv_sales_vw as select a from t;
```

For details about materialized view overview and SQL commands used to refresh and drop materialized views, see the following topics:
+ [Creating materialized views in Amazon Redshift](materialized-view-overview.md)
+ [REFRESH MATERIALIZED VIEW](materialized-view-refresh-sql-command.md)
+ [DROP MATERIALIZED VIEW](materialized-view-drop-sql-command.md)