# ANALYZE<a name="r_ANALYZE"></a>

Updates table statistics for use by the query planner\. 

## Syntax<a name="r_ANALYZE-synopsis"></a>

```
ANALYZE [ VERBOSE ]
[ [ table_name [ ( column_name [, ...] ) ] ]
[ PREDICATE COLUMNS | ALL  COLUMNS ]
```

## Parameters<a name="r_ANALYZE-parameters"></a>

VERBOSE   
A clause that returns progress information messages about the ANALYZE operation\. This option is useful when you don't specify a table\.

 *table\_name*   
You can analyze specific tables, including temporary tables\. You can qualify the table with its schema name\. You can optionally specify a table\_name to analyze a single table\. You can't specify more than one *table\_name* with a single ANALYZE *table\_name* statement\. If you don't specify a *table\_name* value, all of the tables in the currently connected database are analyzed, including the persistent tables in the system catalog\. Amazon Redshift skips analyzing a table if the percentage of rows that have changed since the last ANALYZE is lower than the analyze threshold\. For more information, see [Analyze threshold](#r_ANALYZE-threshold)\.  
You don't need to analyze Amazon Redshift system tables \(STL and STV tables\)\.

 *column\_name*   
If you specify a *table\_name*, you can also specify one or more columns in the table \(as a column\-separated list within parentheses\)\. If a column list is specified, only the listed columns are analyzed\.

 PREDICATE COLUMNS \| ALL COLUMNS   
Clauses that indicates whether ANALYZE should include only predicate columns\. Specify PREDICATE COLUMNS to analyze only columns that have been used as predicates in previous queries or are likely candidates to be used as predicates\. Specify ALL COLUMNS to analyze all columns\. The default is ALL COLUMNS\.   
A column is included in the set of predicate columns if any of the following is true:  
+ The column has been used in a query as a part of a filter, join condition, or group by clause\.
+ The column is a distribution key\.
+ The column is part of a sort key\.
If no columns are marked as predicate columns, for example because the table has not yet been queried, all of the columns are analyzed even when PREDICATE COLUMNS is specified\. For more information about predicate columns, see [Analyzing tables](t_Analyzing_tables.md)\.

## Usage notes<a name="r_ANALYZE-usage-notes"></a>

Amazon Redshift automatically runs ANALYZE on tables that you create with the following commands: 
+ CREATE TABLE AS
+ CREATE TEMP TABLE AS 
+ SELECT INTO

 You can't analyze an external table\.

You don't need to run the ANALYZE command on these tables when they are first created\. If you modify them, you should analyze them in the same way as other tables\.

### Analyze threshold<a name="r_ANALYZE-threshold"></a>

To reduce processing time and improve overall system performance, Amazon Redshift skips ANALYZE for a table if the percentage of rows that have changed since the last ANALYZE command run is lower than the analyze threshold specified by the [analyze\_threshold\_percent](r_analyze_threshold_percent.md) parameter\. By default, `analyze_threshold_percent` is 10\. To change `analyze_threshold_percent` for the current session, execute the [SET](r_SET.md) command\. The following example changes `analyze_threshold_percent` to 20 percent\.

```
set analyze_threshold_percent to 20;
```

To analyze tables when only a small number of rows have changed, set `analyze_threshold_percent` to an arbitrarily small number\. For example, if you set `analyze_threshold_percent` to 0\.01, then a table with 100,000,000 rows aren't skipped if at least 10,000 rows have changed\. 

```
set analyze_threshold_percent to 0.01;
```

If ANALYZE skips a table because it doesn't meet the analyze threshold, Amazon Redshift returns the following message\.

```
ANALYZE SKIP
```

To analyze all tables even if no rows have changed, set `analyze_threshold_percent` to 0\.

To view the results of ANALYZE operations, query the [STL\_ANALYZE](r_STL_ANALYZE.md) system table\. 

For more information about analyzing tables, see [Analyzing tables](t_Analyzing_tables.md)\.

## Examples<a name="r_ANALYZE-examples"></a>

Analyze all of the tables in the TICKIT database and return progress information\.

```
analyze verbose;
```

Analyze the LISTING table only\.

```
analyze listing;
```

Analyze the VENUEID and VENUENAME columns in the VENUE table\. 

```
analyze venue(venueid, venuename);
```

Analyze only predicate columns in the VENUE table\.

```
analyze venue predicate columns;
```