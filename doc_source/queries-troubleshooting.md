# Troubleshooting queries<a name="queries-troubleshooting"></a>

This section provides a quick reference for identifying and addressing some of the most common and most serious issues you are likely to encounter with Amazon Redshift queries\.

**Topics**
+ [Connection fails](#queries-troubleshooting-connection-fails)
+ [Query hangs](#queries-troubleshooting-query-hangs)
+ [Query takes too long](#queries-troubleshooting-query-takes-too-long)
+ [Load fails](#queries-troubleshooting-load-fails)
+ [Load takes too long](#queries-troubleshooting-load-takes-too-long)
+ [Load data is incorrect](#queries-troubleshooting-load-data-incorrect)
+ [Setting the JDBC fetch size parameter](#set-the-JDBC-fetch-size-parameter)

These suggestions give you a starting point for troubleshooting\. You can also refer to the following resources for more detailed information\.
+ [Accessing Amazon Redshift clusters and databases](https://docs.aws.amazon.com/redshift/latest/mgmt/using-rs-tools.html)
+ [Designing tables](t_Creating_tables.md)
+ [Loading data](t_Loading_data.md)
+ [Tutorial: Tuning table design](tutorial-tuning-tables.md)
+ [Tutorial: Loading data from Amazon S3](tutorial-loading-data.md)

## Connection fails<a name="queries-troubleshooting-connection-fails"></a>

Your query connection can fail for the following reasons; we suggest the following troubleshooting approaches\.

**Client cannot connect to server**  
If you are using SSL or server certificates, first remove this complexity while you troubleshoot the connection issue\. Then add SSL or server certificates back when you have found a solution\. For more information, go to [Configure Security Options for Connections](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-ssl-support.html) in the *Amazon Redshift Cluster Management Guide\.*

**Connection is refused**  
Generally, when you receive an error message indicating that there is a failure to establish a connection, it means that there is an issue with the permission to access the cluster\. For more information, go to [The connection is refused or fails](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-refusal-failure-issues.html) in the *Amazon Redshift Cluster Management Guide\.* 

## Query hangs<a name="queries-troubleshooting-query-hangs"></a>

Your query can hang, or stop responding, for the following reasons; we suggest the following troubleshooting approaches\.

**Connection to the database is dropped**  
Reduce the size of maximum transmission unit \(MTU\)\. The MTU size determines the maximum size, in bytes, of a packet that can be transferred in one Ethernet frame over your network connection\. For more information, go to [The connection to the database is dropped](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-drop-issues.html) in the *Amazon Redshift Cluster Management Guide\.* 

**Connection to the database times out**  
Your client connection to the database appears to hang or time out when running long queries, such as a COPY command\. In this case, you might observe that the Amazon Redshift console displays that the query has completed, but the client tool itself still appears to be running the query\. The results of the query might be missing or incomplete depending on when the connection stopped\. This effect happens when idle connections are terminated by an intermediate network component\. For more information, go to [Firewall Timeout Issue](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-firewall-guidance.html) in the *Amazon Redshift Cluster Management Guide\.* 

**Client\-side out\-of\-memory error occurs with ODBC**  
If your client application uses an ODBC connection and your query creates a result set that is too large to fit in memory, you can stream the result set to your client application by using a cursor\. For more information, see [DECLARE](declare.md) and [Performance considerations when using cursors](declare.md#declare-performance)\.

**Client\-side out\-of\-memory error occurs with JDBC**  
When you attempt to retrieve large result sets over a JDBC connection, you might encounter client\-side out\-of\-memory errors\. For more information, see [Setting the JDBC fetch size parameter](#set-the-JDBC-fetch-size-parameter)\.

**There is a potential deadlock**  
If there is a potential deadlock, try the following:
+ View the [STV\_LOCKS](r_STV_LOCKS.md) and [STL\_TR\_CONFLICT](r_STL_TR_CONFLICT.md) system tables to find conflicts involving updates to more than one table\.
+ Use the [PG\_CANCEL\_BACKEND](PG_CANCEL_BACKEND.md) function to cancel one or more conflicting queries\.
+ Use the [PG\_TERMINATE\_BACKEND](PG_TERMINATE_BACKEND.md) function to terminate a session, which forces any currently running transactions in the terminated session to release all locks and roll back the transaction\.
+ Schedule concurrent write operations carefully\. For more information, see [Managing concurrent write operations](c_Concurrent_writes.md)\.

## Query takes too long<a name="queries-troubleshooting-query-takes-too-long"></a>

Your query can take too long for the following reasons; we suggest the following troubleshooting approaches\.

**Tables are not optimized**  
Set the sort key, distribution style, and compression encoding of the tables to take full advantage of parallel processing\. For more information, see [Designing tables](t_Creating_tables.md) and [Tutorial: Tuning table design](tutorial-tuning-tables.md)\.

**Query is writing to disk**  
Your queries might be writing to disk for at least part of the query execution\. For more information, see [Improving query performance](query-performance-improvement-opportunities.md)\.

**Query must wait for other queries to finish**  
You might be able to improve overall system performance by creating query queues and assigning different types of queries to the appropriate queues\. For more information, see [Implementing workload management](cm-c-implementing-workload-management.md)\. 

**Queries are not optimized**  
Analyze the explain plan to find opportunities for rewriting queries or optimizing the database\. For more information, see [Query plan](c-the-query-plan.md)\.

**Query needs more memory to run**  
If a specific query needs more memory, you can increase the available memory by increasing the [wlm\_query\_slot\_count](r_wlm_query_slot_count.md)\. 

**Database requires a VACUUM command to be run**  
Run the VACUUM command whenever you add, delete, or modify a large number of rows, unless you load your data in sort key order\. The VACUUM command reorganizes your data to maintain the sort order and restore performance\. For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\.

## Load fails<a name="queries-troubleshooting-load-fails"></a>

Your data load can fail for the following reasons; we suggest the following troubleshooting approaches\.

**Data Source is in a different AWS Region**  
By default, the Amazon S3 bucket or Amazon DynamoDB table specified in the COPY command must be in the same AWS Region as the cluster\. If your data and your cluster are in different Regions, you receive an error similar to the following: 

```
The bucket you are attempting to access must be addressed using the specified endpoint.
```

If at all possible, make sure your cluster and your data source are the same Region\. You can specify a different Region by using the [REGION](copy-parameters-data-source-s3.md#copy-region) option with the COPY command\. 

**Note**  
If your cluster and your data source are in different AWS Regions, you incur data transfer costs\. You also have higher latency and more issues with eventual consistency\.

**COPY Command Fails**  
Query STL\_LOAD\_ERRORS to discover the errors that occurred during specific loads\. For more information, see [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md)\.

## Load takes too long<a name="queries-troubleshooting-load-takes-too-long"></a>

Your load operation can take too long for the following reasons; we suggest the following troubleshooting approaches\.

**COPY loads data from a single file**  
Split your load data into multiple files\. When you load all the data from a single large file, Amazon Redshift is forced to perform a serialized load, which is much slower\. The number of files should be a multiple of the number of slices in your cluster, and the files should be about equal size, between 1 MB and 1 GB after compression\. For more information, see [Amazon Redshift best practices for designing queries](c_designing-queries-best-practices.md)\.

**Load operation uses multiple COPY commands**  
If you use multiple concurrent COPY commands to load one table from multiple files, Amazon Redshift is forced to perform a serialized load, which is much slower\. In this case, use a single COPY command\.

## Load data is incorrect<a name="queries-troubleshooting-load-data-incorrect"></a>

Your COPY operation can load incorrect data in the following ways; we suggest the following troubleshooting approaches\.

**Not all files are loaded**  
Eventual consistency can cause a discrepancy in some cases between the files listed using an Amazon S3 ListBuckets action and the files available to the COPY command\. For more information, see [Verifying that the data loaded correctly](verifying-that-data-loaded-correctly.md)\.

**Wrong files are loaded**  
Using an object prefix to specify data files can cause unwanted files to be read\. Instead, use a manifest file to specify exactly which files to load\. For more information, see the [copy_from_s3_manifest_file](copy-parameters-data-source-s3.md#copy-manifest-file) option for the COPY command and [Example: COPY from Amazon S3 using a manifest](r_COPY_command_examples.md#copy-command-examples-manifest) in the COPY examples\.

## Setting the JDBC fetch size parameter<a name="set-the-JDBC-fetch-size-parameter"></a>

By default, the JDBC driver collects all the results for a query at one time\. As a result, when you attempt to retrieve a large result set over a JDBC connection, you might encounter a client\-side out\-of\-memory error\. To enable your client to retrieve result sets in batches instead of in a single all\-or\-nothing fetch, set the JDBC fetch size parameter in your client application\.

**Note**  
Fetch size is not supported for ODBC\.

For the best performance, set the fetch size to the highest value that does not lead to out of memory errors\. A lower fetch size value results in more server trips, which prolong execution times\. The server reserves resources, including the WLM query slot and associated memory, until the client retrieves the entire result set or the query is canceled\. When you tune the fetch size appropriately, those resources are released more quickly, making them available to other queries\.

**Note**  
If you need to extract large datasets, we recommend using an [UNLOAD](r_UNLOAD.md) statement to transfer the data to Amazon S3\. When you use UNLOAD, the compute nodes work in parallel to speed up the transfer of data\.

For more information about setting the JDBC fetch size parameter, go to [Getting results based on a cursor](https://jdbc.postgresql.org/documentation/head/query.html#query-with-cursor) in the PostgreSQL documentation\.