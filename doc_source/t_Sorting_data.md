# Choosing sort keys<a name="t_Sorting_data"></a>

When you create a table, you can let Amazon Redshift define the optimal *sort keys* for the table\. To let Amazon Redshift define the sort keys, either don't specify the CREATE TABLE SORTKEY keyword or define SORTKEY AUTO\. When the SORTKEY is set to AUTO, Amazon Redshift might change the sort key of your table data\. For more information, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\. 

When you create a table, you can alternatively define one or more of its columns as *sort keys*\. When data is initially loaded into the empty table, the rows are stored on disk in sorted order\. Information about sort key columns is passed to the query planner, and the planner uses this information to construct plans that exploit the way that the data is sorted\. 

Sorting enables efficient handling of range\-restricted predicates\. Amazon Redshift stores columnar data in 1 MB disk blocks\. The min and max values for each block are stored as part of the metadata\. If query uses a range\-restricted predicate, the query processor can use the min and max values to rapidly skip over large numbers of blocks during table scans\. For example, if a table stores five years of data sorted by date and a query specifies a date range of one month, up to 98 percent of the disk blocks can be eliminated from the scan\. If the data is not sorted, more of the disk blocks \(possibly all of them\) have to be scanned\. 

You can specify either a compound or interleaved sort key\. A compound sort key is more efficient when query predicates use a *prefix*, which is a subset of the sort key columns in order\. An interleaved sort key gives equal weight to each column in the sort key, so query predicates can use any subset of the columns that make up the sort key, in any order\. For examples of using compound sort keys and interleaved sort keys, see [Comparing sort styles](t_Sorting_data-compare-sort-styles.md)\.

To understand the impact of the chosen sort key on query performance, use the [EXPLAIN](r_EXPLAIN.md) command\. For more information, see [Query planning and execution workflow](c-query-planning.md)\. 

To define a sort type, use either the INTERLEAVED or COMPOUND keyword with your CREATE TABLE or CREATE TABLE AS statement\. The default is COMPOUND\. The default COMPOUND is recommended unless your tables aren't updated regularly with INSERT, UPDATE, or DELETE\. An INTERLEAVED sort key can use a maximum of eight columns\. Depending on your data and cluster size, VACUUM REINDEX takes significantly longer than VACUUM FULL because it makes an additional pass to analyze the interleaved sort keys\. The sort and merge operation can take longer for interleaved tables because the interleaved sort might need to rearrange more rows than a compound sort\.

To view the sort keys for a table, query the [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md) system view\.

**Topics**
+ [Compound sort key](#t_Sorting_data-compound)
+ [Interleaved sort key](#t_Sorting_data-interleaved)
+ [Comparing sort styles](t_Sorting_data-compare-sort-styles.md)

## Compound sort key<a name="t_Sorting_data-compound"></a>

 A compound key is made up of all of the columns listed in the sort key definition, in the order they are listed\. A compound sort key is most useful when a query's filter applies conditions, such as filters and joins, that use a prefix of the sort keys\. The performance benefits of compound sorting decrease when queries depend only on secondary sort columns, without referencing the primary columns\. COMPOUND is the default sort type\.

Compound sort keys might speed up joins, GROUP BY and ORDER BY operations, and window functions that use PARTITION BY and ORDER BY\. For example, a merge join, which is often faster than a hash join, is feasible when the data is distributed and presorted on the joining columns\. Compound sort keys also help improve compression\. 

As you add rows to a sorted table that already contains data, the unsorted region grows, which has a significant effect on performance\. The effect is greater when the table uses interleaved sorting, especially when the sort columns include data that increases monotonically, such as date or timestamp columns\. You should run a VACUUM operation regularly, especially after large data loads, to re\-sort and re\-analyze the data\. For more information, see [Managing the size of the unsorted region](r_vacuum_diskspacereqs.md)\. After vacuuming to resort the data, it's a good practice to run an ANALYZE command to update the statistical metadata for the query planner\. For more information, see [Analyzing tables](t_Analyzing_tables.md)\.

## Interleaved sort key<a name="t_Sorting_data-interleaved"></a>

An interleaved sort gives equal weight to each column, or subset of columns, in the sort key\. If multiple queries use different columns for filters, then you can often improve performance for those queries by using an interleaved sort style\. When a query uses restrictive predicates on secondary sort columns, interleaved sorting significantly improves query performance as compared to compound sorting\. 

**Important**  
Don't use an interleaved sort key on columns with monotonically increasing attributes, such as identity columns, dates, or timestamps\.

The performance improvements you gain by implementing an interleaved sort key should be weighed against increased load and vacuum times\. 

Interleaved sorts are most effective with highly selective queries that filter on one or more of the sort key columns in the WHERE clause, for example `select c_name from customer where c_region = 'ASIA'`\. The benefits of interleaved sorting increase with the number of sorted columns that are restricted\. 

An interleaved sort is more effective with large tables\. Sorting is applied on each slice, so an interleaved sort is most effective when a table is large enough to require multiple 1 MB blocks per slice and the query processor is able to skip a significant proportion of the blocks using restrictive predicates\. To view the number of blocks a table uses, query the [STV\_BLOCKLIST](r_STV_BLOCKLIST.md) system view\.

 When sorting on a single column, an interleaved sort might give better performance than a compound sort if the column values have a long common prefix\. For example, URLs commonly begin with "http://www"\. Compound sort keys use a limited number of characters from the prefix, which results in a lot of duplication of keys\. Interleaved sorts use an internal compression scheme for zone map values that enables them to better discriminate among column values that have a long common prefix\.
<a name="t_Sorting_data-interleaved-reindex"></a>
**VACUUM REINDEX**  
As you add rows to a sorted table that already contains data, performance might deteriorate over time\. This deterioration occurs for both compound and interleaved sorts, but it has a greater effect on interleaved tables\. A VACUUM restores the sort order, but the operation can take longer for interleaved tables because merging new interleaved data might involve modifying every data block\.

When tables are initially loaded, Amazon Redshift analyzes the distribution of the values in the sort key columns and uses that information for optimal interleaving of the sort key columns\. As a table grows, the distribution of the values in the sort key columns can change, or skew, especially with date or timestamp columns\. If the skew becomes too large, performance might be affected\. To re\-analyze the sort keys and restore performance, run the VACUUM command with the REINDEX key word\. Because it needs to take an extra analysis pass over the data, VACUUM REINDEX can take longer than a standard VACUUM for interleaved tables\. To view information about key distribution skew and last reindex time, query the [SVV\_INTERLEAVED\_COLUMNS](r_SVV_INTERLEAVED_COLUMNS.md) system view\.

For more information about how to determine how often to run VACUUM and when to run a VACUUM REINDEX, see [Deciding whether to reindex](r_vacuum-decide-whether-to-reindex.md)\. 