# Troubleshooting Queries<a name="queries-troubleshooting"></a>

This section provides a quick reference for identifying and addressing some of the most common and most serious issues you are likely to encounter with Amazon Redshift queries\.


+ [Connection Fails](#queries-troubleshooting-connection-fails)
+ [Query Hangs](#queries-troubleshooting-query-hangs)
+ [Query Takes Too Long](#queries-troubleshooting-query-takes-too-long)
+ [Load Fails](#queries-troubleshooting-load-fails)
+ [Load Takes Too Long](#queries-troubleshooting-load-takes-too-long)
+ [Load Data Is Incorrect](#queries-troubleshooting-load-data-incorrect)
+ [Setting the JDBC Fetch Size Parameter](#set-the-JDBC-fetch-size-parameter)

These suggestions give you a starting point for troubleshooting\. You can also refer to the following resources for more detailed information\.

+ [Accessing Amazon Redshift Clusters and Databases](http://docs.aws.amazon.com/redshift/latest/mgmt/using-rs-tools.html)

+ [Designing Tables](t_Creating_tables.md)

+ [Loading Data](t_Loading_data.md)

+ [Tutorial: Tuning Table Design](tutorial-tuning-tables.md)

+ [Tutorial: Loading Data from Amazon S3](tutorial-loading-data.md)

## Connection Fails<a name="queries-troubleshooting-connection-fails"></a>

Your query connection can fail for the following reasons; we suggest the following troubleshooting approaches\.

**Client Cannot Connect to Server**  
If you are using SSL or server certificates, first remove this complexity while you troubleshoot the connection issue\. Then add SSL or server certificates back when you have found a solution\. For more information, go to [Configure Security Options for Connections](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-ssl-support.html) in the *Amazon Redshift Cluster Management Guide\.*

**Connection Is Refused**  
Generally, when you receive an error message indicating that there is a failure to establish a connection, it means that there is an issue with the permission to access the cluster\. For more information, go to [The connection is refused or fails](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-refusal-failure-issues.html) in the *Amazon Redshift Cluster Management Guide\.* 

## Query Hangs<a name="queries-troubleshooting-query-hangs"></a>

Your query can hang, or stop responding, for the following reasons; we suggest the following troubleshooting approaches\.

**Connection to the Database Is Dropped**  
Reduce the size of maximum transmission unit \(MTU\)\. The MTU size determines the maximum size, in bytes, of a packet that can be transferred in one Ethernet frame over your network connection\. For more information, go to [The connection to the database is dropped](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-drop-issues.html) in the *Amazon Redshift Cluster Management Guide\.* 

**Connection to the Database Times Out**  
Your client connection to the database appears to hang or timeout when running long queries, such as a COPY command\. In this case, you might observe that the Amazon Redshift console displays that the query has completed, but the client tool itself still appears to be running the query\. The results of the query might be missing or incomplete depending on when the connection stopped\. This effect happens when idle connections are terminated by an intermediate network component\. For more information, go to [Firewall Timeout Issue](http://docs.aws.amazon.com/redshift/latest/mgmt/connecting-firewall-guidance.html) in the *Amazon Redshift Cluster Management Guide\.* 

**Client\-Side Out\-of\-Memory Error Occurs with ODBC**  
If your client application uses an ODBC connection and your query creates a result set that is too large to fit in memory, you can stream the result set to your client application by using a cursor\. For more information, see [DECLARE](declare.md) and [Performance Considerations When Using Cursors](declare.md#declare-performance)\.

**Client\-Side Out\-of\-Memory Error Occurs with JDBC**  
When you attempt to retrieve large result sets over a JDBC connection, you might encounter client\-side out\-of\-memory errors\. For more information, see [Setting the JDBC fetch size parameter](http://docs.aws.amazon.com/redshift/latest/dg/jdbc-fetch-size-parameter.html)\.

**There Is a Potential Deadlock**  
If there is a potential deadlock, try the following:

+ View the [STV\_LOCKS](r_STV_LOCKS.md) and [STL\_TR\_CONFLICT](r_STL_TR_CONFLICT.md) system tables to find conflicts involving updates to more than one table\.

+ Use the [PG\_CANCEL\_BACKEND](PG_CANCEL_BACKEND.md) function to cancel one or more conflicting queries\.

+ Use the [PG\_TERMINATE\_BACKEND](PG_TERMINATE_BACKEND.md) function to terminate a session, which forces any currently running transactions in the terminated session to release all locks and roll back the transaction\.

+ Schedule concurrent write operations carefully\. For more information, see [Managing Concurrent Write Operations](c_Concurrent_writes.md)\.

## Query Takes Too Long<a name="queries-troubleshooting-query-takes-too-long"></a>

Your query can take too long for the following reasons; we suggest the following troubleshooting approaches\.

**Tables Are Not Optimized**  
Set the sort key, distribution style, and compression encoding of the tables to take full advantage of parallel processing\. For more information, see [Designing Tables](t_Creating_tables.md) and [Tutorial: Tuning Table Design](tutorial-tuning-tables.md)\.

**Query Is Writing to Disk**  
Your queries might be writing to disk for at least part of the query execution\. For more information, see [Improving Query Performance](query-performance-improvement-opportunities.md)\.

**Query Must Wait for Other Queries to Finish**  
You might be able to improve overall system performance by creating query queues and assigning different types of queries to the appropriate queues\. For more information, see [Implementing Workload Management](cm-c-implementing-workload-management.md)\. 

**Queries Are Not Optimized**  
Analyze the explain plan to find opportunities for rewriting queries or optimizing the database\. For more information, see [Query Plan](c-the-query-plan.md)\.

**Query Needs More Memory to Run**  
If a specific query needs more memory, you can increase the available memory by increasing the [wlm\_query\_slot\_count](r_wlm_query_slot_count.md)\. 

**Database Requires a VACUUM Command to Be Run**  
Run the VACUUM command whenever you add, delete, or modify a large number of rows, unless you load your data in sort key order\. The VACUUM command reorganizes your data to maintain the sort order and restore performance\. For more information, see [Vacuuming Tables](t_Reclaiming_storage_space202.md)\.

## Load Fails<a name="queries-troubleshooting-load-fails"></a>

Your data load can fail for the following reasons; we suggest the following troubleshooting approaches\.

**Data Source Is in a Different Region**  
By default, the Amazon S3 bucket or Amazon DynamoDB table specified in the COPY command must be in the same region as the cluster\. If your data and your cluster are in different regions, you will receive an error similar to the following: 

```
The bucket you are attempting to access must be addressed using the specified endpoint.
```

If at all possible, make sure your cluster and your data source are the same region\. You can specify a different region by using the [REGION](copy-parameters-data-source-s3.md#copy-region) option with the COPY command\. 

**Note**  
If your cluster and your data source are in different AWS regions, you will incur data transfer costs\. You will also have higher latency and more issues with eventual consistency\.

**COPY Command Fails**  
Query STL\_LOAD\_ERRORS to discover the errors that occurred during specific loads\. For more information, see [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md)\.

## Load Takes Too Long<a name="queries-troubleshooting-load-takes-too-long"></a>

Your load operation can take too long for the following reasons; we suggest the following troubleshooting approaches\.

**COPY Loads Data from a Single File**  
Split your load data into multiple files\. When you load all the data from a single large file, Amazon Redshift is forced to perform a serialized load, which is much slower\. The number of files should be a multiple of the number of slices in your cluster, and the files should be about equal size, between 1 MB and 1 GB after compression\. For more information, see [Best Practices for Designing Queries](c_designing-queries-best-practices.md)\.

**Load Operation Uses Multiple COPY Commands**  
If you use multiple concurrent COPY commands to load one table from multiple files, Amazon Redshift is forced to perform a serialized load, which is much slower\. In this case, use a single COPY command\.

## Load Data Is Incorrect<a name="queries-troubleshooting-load-data-incorrect"></a>

Your COPY operation can load incorrect data in the following ways; we suggest the following troubleshooting approaches\.

**Not All Files Are Loaded**  
Eventual consistency can cause a discrepancy in some cases between the files listed using an Amazon S3 ListBuckets action and the files available to the COPY command\. For more information, see [Verifying That the Data Was Loaded Correctly](verifying-that-data-loaded-correctly.md)\.

**Wrong Files Are Loaded**  
Using an object prefix to specify data files can cause unwanted files to be read\. Instead, use a manifest file to specify exactly which files to load\. For more information, see the [copy_from_s3_manifest_file](copy-parameters-data-source-s3.md#copy-manifest-file) option for the COPY command and [Example: COPY from Amazon S3 using a manifest](r_COPY_command_examples.md#copy-command-examples-manifest) in the COPY examples\.

## Setting the JDBC Fetch Size Parameter<a name="set-the-JDBC-fetch-size-parameter"></a>

By default, the JDBC driver collects all the results for a query at one time\. As a result, when you attempt to retrieve a large result set over a JDBC connection, you might encounter a client\-side out\-of\-memory error\. To enable your client to retrieve result sets in batches instead of in a single all\-or\-nothing fetch, set the JDBC fetch size parameter in your client application\.

**Note**  
Fetch size is not supported for ODBC\.

For the best performance, set the fetch size to the highest value that does not lead to out of memory errors\. A lower fetch size value results in more server trips, which prolongs execution times\. The server reserves resources, including the WLM query slot and associated memory, until the client retrieves the entire result set or the query is canceled\. When you tune the fetch size appropriately, those resources are released more quickly, making them available to other queries\.

**Note**  
If you need to extract large datasets, we recommend using an [UNLOAD](r_UNLOAD.md) statement to transfer the data to Amazon S3\. When you use UNLOAD, the compute nodes work in parallel to speed up the transfer of data\.

For more information about setting the JDBC fetch size parameter, go to [Getting results based on a cursor](http://jdbc.postgresql.org/documentation/head/query.html#query-with-cursor) in the PostgreSQL documentation\.