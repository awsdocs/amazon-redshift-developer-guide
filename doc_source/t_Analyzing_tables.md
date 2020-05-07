# Analyzing tables<a name="t_Analyzing_tables"></a>

The ANALYZE operation updates the statistical metadata that the query planner uses to choose optimal plans\.

In most cases, you don't need to explicitly run the ANALYZE command\. Amazon Redshift monitors changes to your workload and automatically updates statistics in the background\. In addition, the COPY command performs an analysis automatically when it loads data into an empty table\. 

To explicitly analyze a table or the entire database, run the [ANALYZE](r_ANALYZE.md) command\. 

**Topics**
+ [Automatic analyze](#t_Analyzing_tables-auto-analyze)
+ [Analysis of new table data](#t_Analyzing_tables-new-tables)
+ [ANALYZE command history](c_check_last_analyze.md)

## Automatic analyze<a name="t_Analyzing_tables-auto-analyze"></a>

Amazon Redshift continuously monitors your database and automatically performs analyze operations in the background\. To minimize impact to your system performance, automatic analyze runs during periods when workloads are light\. 

Automatic analyze is enabled by default\. To disable automatic analyze, set the `auto_analyze` parameter to **false** by modifying your cluster's parameter group\. 

To reduce processing time and improve overall system performance, Amazon Redshift skips automatic analyze for any table where the extent of modifications is small\. 

An analyze operation skips tables that have up\-to\-date statistics\. If you run ANALYZE as part of your extract, transform, and load \(ETL\) workflow, automatic analyze skips tables that have current statistics\. Similarly, an explicit ANALYZE skips tables when automatic analyze has updated the table's statistics\. 

## Analysis of new table data<a name="t_Analyzing_tables-new-tables"></a>

 By default, the COPY command performs an ANALYZE after it loads data into an empty table\. You can force an ANALYZE regardless of whether a table is empty by setting STATUPDATE ON\. If you specify STATUPDATE OFF, an ANALYZE is not performed\. Only the table owner or a superuser can run the ANALYZE command or run the COPY command with STATUPDATE set to ON\.

Amazon Redshift also analyzes new tables that you create with the following commands:
+ CREATE TABLE AS \(CTAS\) 
+ CREATE TEMP TABLE AS 
+ SELECT INTO 

Amazon Redshift returns a warning message when you run a query against a new table that was not analyzed after its data was initially loaded\. No warning occurs when you query a table after a subsequent update or load\. The same warning message is returned when you run the EXPLAIN command on a query that references tables that have not been analyzed\.

Whenever adding data to a nonempty table significantly changes the size of the table, you can explicitly update statistics\. You do so either by running an ANALYZE command or by using the STATUPDATE ON option with the COPY command\. To view details about the number of rows that have been inserted or deleted since the last ANALYZE, query the [PG\_STATISTIC\_INDICATOR](r_PG_STATISTIC_INDICATOR.md) system catalog table\. 

You can specify the scope of the [ANALYZE](r_ANALYZE.md) command to one of the following: 
+ The entire current database
+ A single table
+ One or more specific columns in a single table
+ Columns that are likely to be used as predicates in queries

 The ANALYZE command gets a sample of rows from the table, does some calculations, and saves resulting column statistics\. By default, Amazon Redshift runs a sample pass for the DISTKEY column and another sample pass for all of the other columns in the table\. If you want to generate statistics for a subset of columns, you can specify a comma\-separated column list\. You can run ANALYZE with the PREDICATE COLUMNS clause to skip columns that aren’t used as predicates\.

 ANALYZE operations are resource intensive, so run them only on tables and columns that actually require statistics updates\. You don't need to analyze all columns in all tables regularly or on the same schedule\. If the data changes substantially, analyze the columns that are frequently used in the following:
+ Sorting and grouping operations
+ Joins
+ Query predicates

To reduce processing time and improve overall system performance, Amazon Redshift skips ANALYZE for any table that has a low percentage of changed rows, as determined by the [analyze\_threshold\_percent](r_analyze_threshold_percent.md) parameter\. By default, the analyze threshold is set to 10 percent\. You can change the analyze threshold for the current session by running a [SET](r_SET.md) command\.

Columns that are less likely to require frequent analysis are those that represent facts and measures and any related attributes that are never actually queried, such as large VARCHAR columns\. For example, consider the LISTING table in the TICKIT database\.

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'listing';

column         |        type        | encoding | distkey | sortkey 
---------------+--------------------+----------+---------+---------
listid         | integer            | none     | t       | 1       
sellerid       | integer            | none     | f       | 0       
eventid        | integer            | mostly16 | f       | 0       
dateid         | smallint           | none     | f       | 0       
numtickets     | smallint           | mostly8  | f       | 0       
priceperticket | numeric(8,2)       | bytedict | f       | 0       
totalprice     | numeric(8,2)       | mostly32 | f       | 0       
listtime       | timestamp with...  | none     | f       | 0
```

If this table is loaded every day with a large number of new records, the LISTID column, which is frequently used in queries as a join key, needs to be analyzed regularly\. If TOTALPRICE and LISTTIME are the frequently used constraints in queries, you can analyze those columns and the distribution key on every weekday\.

```
analyze listing(listid, totalprice, listtime);
```

Suppose that the sellers and events in the application are much more static, and the date IDs refer to a fixed set of days covering only two or three years\. In this case,the unique values for these columns don't change significantly\. However, the number of instances of each unique value will increase steadily\. 

In addition, consider the case where the NUMTICKETS and PRICEPERTICKET measures are queried infrequently compared to the TOTALPRICE column\. In this case, you can run the ANALYZE command on the whole table once every weekend to update statistics for the five columns that are not analyzed daily: 
<a name="t_Analyzing_tables-predicate-columns"></a>
**Predicate columns**  
As a convenient alternative to specifying a column list, you can choose to analyze only the columns that are likely to be used as predicates\. When you run a query, any columns that are used in a join, filter condition, or group by clause are marked as predicate columns in the system catalog\. When you run ANALYZE with the PREDICATE COLUMNS clause, the analyze operation includes only columns that meet the following criteria:
+ The column is marked as a predicate column\.
+ The column is a distribution key\.
+ The column is part of a sort key\.

If none of a table's columns are marked as predicates, ANALYZE includes all of the columns, even when PREDICATE COLUMNS is specified\. If no columns are marked as predicate columns, it might be because the table has not yet been queried\. 

You might choose to use PREDICATE COLUMNS when your workload's query pattern is relatively stable\. When the query pattern is variable, with different columns frequently being used as predicates, using PREDICATE COLUMNS might temporarily result in stale statistics\. Stale statistics can lead to suboptimal query execution plans and long execution times\. However, the next time you run ANALYZE using PREDICATE COLUMNS, the new predicate columns are included\. 

To view details for predicate columns, use the following SQL to create a view named PREDICATE\_COLUMNS\. 

```
CREATE VIEW predicate_columns AS
WITH predicate_column_info as (
SELECT ns.nspname AS schema_name, c.relname AS table_name, a.attnum as col_num,  a.attname as col_name,
        CASE
            WHEN 10002 = s.stakind1 THEN array_to_string(stavalues1, '||') 
            WHEN 10002 = s.stakind2 THEN array_to_string(stavalues2, '||')
            WHEN 10002 = s.stakind3 THEN array_to_string(stavalues3, '||')
            WHEN 10002 = s.stakind4 THEN array_to_string(stavalues4, '||')
            ELSE NULL::varchar
        END AS pred_ts
   FROM pg_statistic s
   JOIN pg_class c ON c.oid = s.starelid
   JOIN pg_namespace ns ON c.relnamespace = ns.oid
   JOIN pg_attribute a ON c.oid = a.attrelid AND a.attnum = s.staattnum)
SELECT schema_name, table_name, col_num, col_name,
       pred_ts NOT LIKE '2000-01-01%' AS is_predicate,
       CASE WHEN pred_ts NOT LIKE '2000-01-01%' THEN (split_part(pred_ts, '||',1))::timestamp ELSE NULL::timestamp END as first_predicate_use,
       CASE WHEN pred_ts NOT LIKE '%||2000-01-01%' THEN (split_part(pred_ts, '||',2))::timestamp ELSE NULL::timestamp END as last_analyze
FROM predicate_column_info;
```

Suppose you run the following query against the LISTING table\. Note that LISTID, LISTTIME, and EVENTID are used in the join, filter, and group by clauses\.

```
select s.buyerid,l.eventid, sum(l.totalprice)
from listing l
join sales s on l.listid = s.listid
where l.listtime > '2008-12-01'
group by l.eventid, s.buyerid;
```

When you query the PREDICATE\_COLUMNS view, as shown in the following example, you see that LISTID, EVENTID, and LISTTIME are marked as predicate columns\.

```
select * from predicate_columns 
where table_name = 'listing';
```

```
schema_name | table_name | col_num | col_name       | is_predicate | first_predicate_use | last_analyze       
------------+------------+---------+----------------+--------------+---------------------+--------------------
public      | listing    |       1 | listid         | true         | 2017-05-05 19:27:59 | 2017-05-03 18:27:41
public      | listing    |       2 | sellerid       | false        |                     | 2017-05-03 18:27:41
public      | listing    |       3 | eventid        | true         | 2017-05-16 20:54:32 | 2017-05-03 18:27:41
public      | listing    |       4 | dateid         | false        |                     | 2017-05-03 18:27:41
public      | listing    |       5 | numtickets     | false        |                     | 2017-05-03 18:27:41
public      | listing    |       6 | priceperticket | false        |                     | 2017-05-03 18:27:41
public      | listing    |       7 | totalprice     | false        |                     | 2017-05-03 18:27:41
public      | listing    |       8 | listtime       | true         | 2017-05-16 20:54:32 | 2017-05-03 18:27:41
```

Keeping statistics current improves query performance by enabling the query planner to choose optimal plans\. Amazon Redshift refreshes statistics automatically in the background, and you can also explicitly run the ANALYZE command\. If you choose to explicitly run ANALYZE, do the following:
+ Run the ANALYZE command before running queries\.
+ Run the ANALYZE command on the database routinely at the end of every regular load or update cycle\.
+ Run the ANALYZE command on any new tables that you create and any existing tables or columns that undergo significant change\.
+ Consider running ANALYZE operations on different schedules for different types of tables and columns, depending on their use in queries and their propensity to change\.
+ To save time and cluster resources, use the PREDICATE COLUMNS clause when you run ANALYZE\.

An analyze operation skips tables that have up\-to\-date statistics\. If you run ANALYZE as part of your extract, transform, and load \(ETL\) workflow, automatic analyze skips tables that have current statistics\. Similarly, an explicit ANALYZE skips tables when automatic analyze has updated the table's statistics\.