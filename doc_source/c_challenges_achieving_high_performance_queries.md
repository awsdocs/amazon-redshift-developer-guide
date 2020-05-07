# Performance<a name="c_challenges_achieving_high_performance_queries"></a>

Amazon Redshift achieves extremely fast query execution by employing these performance features\.

**Topics**
+ [Massively parallel processing](#massively-parallel-processing)
+ [Columnar data storage](#columnar-data-storage)
+ [Data compression](#data-compression)
+ [Query optimizer](#query-optimizer)
+ [Result caching](#result-caching)
+ [Compiled code](#compiled-code)

## Massively parallel processing<a name="massively-parallel-processing"></a>

Massively parallel processing \(MPP\) enables fast execution of the most complex queries operating on large amounts of data\. Multiple compute nodes handle all query processing leading up to final result aggregation, with each core of each node executing the same compiled query segments on portions of the entire data\.

Amazon Redshift distributes the rows of a table to the compute nodes so that the data can be processed in parallel\. By selecting an appropriate distribution key for each table, you can optimize the distribution of data to balance the workload and minimize movement of data from node to node\. For more information, see [Choose the best distribution style](c_best-practices-best-dist-key.md)\.

Loading data from flat files takes advantage of parallel processing by spreading the workload across multiple nodes while simultaneously reading from multiple files\. For more information about how to load data into tables, see [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md)\.

## Columnar data storage<a name="columnar-data-storage"></a>

Columnar storage for database tables drastically reduces the overall disk I/O requirements and is an important factor in optimizing analytic query performance\. Storing database table information in a columnar fashion reduces the number of disk I/O requests and reduces the amount of data you need to load from disk\. Loading less data into memory enables Amazon Redshift to perform more in\-memory processing when executing queries\. See [Columnar storage](c_columnar_storage_disk_mem_mgmnt.md) for a more detailed explanation\.

When columns are sorted appropriately, the query processor is able to rapidly filter out a large subset of data blocks\. For more information, see [Choose the best sort key](c_best-practices-sort-key.md)\.

## Data compression<a name="data-compression"></a>

Data compression reduces storage requirements, thereby reducing disk I/O, which improves query performance\. When you execute a query, the compressed data is read into memory, then uncompressed during query execution\. Loading less data into memory enables Amazon Redshift to allocate more memory to analyzing the data\. Because columnar storage stores similar data sequentially, Amazon Redshift is able to apply adaptive compression encodings specifically tied to columnar data types\. The best way to enable data compression on table columns is by allowing Amazon Redshift to apply optimal compression encodings when you load the table with data\. To learn more about using automatic data compression, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.

## Query optimizer<a name="query-optimizer"></a>

The Amazon Redshift query execution engine incorporates a query optimizer that is MPP\-aware and also takes advantage of the columnar\-oriented data storage\. The Amazon Redshift query optimizer implements significant enhancements and extensions for processing complex analytic queries that often include multi\-table joins, subqueries, and aggregation\. To learn more about optimizing queries, see [Tuning query performance](c-optimizing-query-performance.md)\.

## Result caching<a name="result-caching"></a>

To reduce query execution time and improve system performance, Amazon Redshift caches the results of certain types of queries in memory on the leader node\. When a user submits a query, Amazon Redshift checks the results cache for a valid, cached copy of the query results\. If a match is found in the result cache, Amazon Redshift uses the cached results and doesn't execute the query\. Result caching is transparent to the user\.

Result caching is enabled by default\. To disable result caching for the current session, set the [enable\_result\_cache\_for\_session](r_enable_result_cache_for_session.md) parameter to `off`\.

Amazon Redshift uses cached results for a new query when all of the following are true:
+ The user submitting the query has access privilege to the objects used in the query\.
+ The table or views in the query haven't been modified\.
+ The query doesn't use a function that must be evaluated each time it's run, such as GETDATE\.
+ The query doesn't reference Amazon Redshift Spectrum external tables\.
+ Configuration parameters that might affect query results are unchanged\.
+ The query syntactically matches the cached query\.

To maximize cache effectiveness and efficient use of resources, Amazon Redshift doesn't cache some large query result sets\. Amazon Redshift determines whether to cache query results based on a number of factors\. These factors include the number of entries in the cache and the instance type of your Amazon Redshift cluster\. 

To determine whether a query used the result cache, query the [SVL\_QLOG](r_SVL_QLOG.md) system view\. If a query used the result cache, the source\_query column returns the query ID of the source query\. If result caching wasn't used, the source\_query column value is NULL\. 

The following example shows that queries submitted by userid 104 and userid 102 use the result cache from queries run by userid 100\.

```
select userid, query, elapsed, source_query from svl_qlog 
where userid > 1
order by query desc;

userid | query  | elapsed  | source_query
-------+--------+----------+-------------
   104 | 629035 |       27 |       628919
   104 | 629034 |       60 |       628900
   104 | 629033 |       23 |       628891
   102 | 629017 |  1229393 |             
   102 | 628942 |       28 |       628919
   102 | 628941 |       57 |       628900
   102 | 628940 |       26 |       628891
   100 | 628919 | 84295686 |             
   100 | 628900 | 87015637 |             
   100 | 628891 | 58808694 |
```

For details about the queries used to create the results shown in the previous example, see [Step 2: Test system performance to establish a baseline](tutorial-tuning-tables-test-performance.md) in the [Tuning Table Design](tutorial-tuning-tables.md) tutorial\.

## Compiled code<a name="compiled-code"></a>

The leader node distributes fully optimized compiled code across all of the nodes of a cluster\. Compiling the query eliminates the overhead associated with an interpreter and therefore increases the execution speed, especially for complex queries\. The compiled code is cached and shared across sessions on the same cluster, so subsequent executions of the same query will be faster, often even with different parameters\. 

The execution engine compiles different code for the JDBC connection protocol and for ODBC and psql \(libq\) connection protocols, so two clients using different protocols will each incur the first\-time cost of compiling the code\. Other clients that use the same protocol, however, will benefit from sharing the cached code\.