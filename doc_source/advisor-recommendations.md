# Amazon Redshift Advisor recommendations<a name="advisor-recommendations"></a>

Amazon Redshift Advisor offers recommendations about how to optimize your Amazon Redshift cluster to increase performance and save on operating costs\. You can find explanations for each recommendation in the console, as described preceding\. You can find further details on these recommendations in the following sections\. 

**Topics**
+ [Compress table data](#cluster-compression-recommendation)
+ [Compress Amazon S3 file objects loaded by COPY](#cluster-compress-s3-recommendation)
+ [Isolate multiple active databases](#isolate-active-dbs-recommendation)
+ [Reallocate workload management \(WLM\) memory](#reallocate-wlm-recommendation)
+ [Skip compression analysis during COPY](#skip-compression-analysis-recommendation)
+ [Split Amazon S3 objects loaded by COPY](#split-s3-objects-recommendation)
+ [Update table statistics](#update-table-statistics-recommendation)
+ [Enable short query acceleration](#enable-sqa-recommendation)
+ [Replace single\-column interleaved sort keys](#single-column-interleaved-sort-recommendation)
+ [Alter distribution keys on tables](#alter-diststyle-distkey-recommendation)
+ [Alter sort keys on tables](#alter-sortkey-recommendation)

## Compress table data<a name="cluster-compression-recommendation"></a>

Amazon Redshift is optimized to reduce your storage footprint and improve query performance by using compression encodings\. When you don't use compression, data consumes additional space and requires additional disk I/O\. Applying compression to large uncompressed columns can have a big impact on your cluster\. 

**Analysis**

The compression analysis in Advisor tracks uncompressed storage allocated to permanent user tables\. It reviews storage metadata associated with large uncompressed columns that aren't sort key columns\. Advisor offers a recommendation to rebuild tables with uncompressed columns when the total amount of uncompressed storage exceeds 15 percent of total storage space, or at the following node\-specific thresholds\. 


| Cluster Size | Threshold | 
| --- | --- | 
| DC2\.LARGE | 480 GB | 
| DC2\.8XLARGE | 2\.56 TB | 
| DS2\.XLARGE | 4 TB | 
| DS2\.8XLAGE | 16 TB | 

**Recommendation**

Addressing uncompressed storage for a single table is a one\-time optimization that requires the table to be rebuilt\. We recommend that you rebuild any tables that contain uncompressed columns that are both large and frequently accessed\. To identify which tables contain the most uncompressed storage, run the following SQL command as a superuser\.

```
SELECT
       ti.schema||'.'||ti."table" tablename,
       raw_size.size uncompressed_mb,
       ti.size total_mb
     FROM svv_table_info ti
     LEFT JOIN (
       SELECT tbl table_id, COUNT(*) size
       FROM stv_blocklist
       WHERE (tbl,col) IN (
         SELECT attrelid, attnum-1
         FROM pg_attribute
         WHERE attencodingtype IN (0,128)
         AND attnum>0 AND attsortkeyord != 1)
       GROUP BY tbl) raw_size USING (table_id)
     WHERE raw_size.size IS NOT NULL
     ORDER BY raw_size.size DESC;
```

The data returned in the `uncompressed_mb` column represents the total number of uncompressed 1\-MB blocks for all columns in the table\. 

When you rebuild the tables, use the `ENCODE` parameter to explicitly set column compression\.

**Implementation tips**
+ Leave any columns that are the first column in a compound sort key uncompressed\. The Advisor analysis doesn't count the storage consumed by those columns\.
+ Compressing large columns has a higher impact on performance and storage than compressing small columns\.
+ If you are unsure which compression is best, use the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command to suggest a compression\.
+ To generate the data definition language \(DDL\) statements for existing tables, you can use the AWS [Generate Table DDL](https://github.com/awslabs/amazon-redshift-utils/blob/master/src/AdminViews/v_generate_tbl_ddl.sql) utility, found on GitHub\.
+ To simplify the compression suggestions and the process of rebuilding tables, you can use the [Amazon Redshift Column Encoding Utility](https://github.com/awslabs/amazon-redshift-utils/tree/master/src/ColumnEncodingUtility), found on GitHub\.

## Compress Amazon S3 file objects loaded by COPY<a name="cluster-compress-s3-recommendation"></a>

The COPY command takes advantage of the massively parallel processing \(MPP\) architecture in Amazon Redshift to read and load data in parallel\. It can read files from Amazon S3, DynamoDB tables, and text output from one or more remote hosts\. 

When loading large amounts of data, we strongly recommend using the COPY command to load compressed data files from S3\. Compressing large datasets saves time uploading the files to S3\. COPY can also speed up the load process by uncompressing the files as they are read\. 

**Analysis**

Long\-running COPY commands that load large uncompressed datasets often have an opportunity for considerable performance improvement\. The Advisor analysis identifies COPY commands that load large uncompressed datasets\. In such a case, Advisor generates a recommendation to implement compression on the source files in S3\. 

**Recommendation**

Ensure that each COPY that loads a significant amount of data, or runs for a significant duration, ingests compressed data objects from S3\. You can identify the COPY commands that load large uncompressed datasets from S3 by running the following SQL command as a superuser\.

```
SELECT
    wq.userid, query, exec_start_time AS starttime, COUNT(*) num_files,
    ROUND(MAX(wq.total_exec_time/1000000.0),2) execution_secs,
    ROUND(SUM(transfer_size)/(1024.0*1024.0),2) total_mb,
    SUBSTRING(querytxt,1,60) copy_sql
FROM stl_s3client s
JOIN stl_query q USING (query)
JOIN stl_wlm_query wq USING (query)
WHERE s.userid>1 AND http_method = 'GET'
    AND POSITION('COPY ANALYZE' IN querytxt) = 0
    AND aborted = 0 AND final_state='Completed'
GROUP BY 1, 2, 3, 7
HAVING SUM(transfer_size) = SUM(data_size) 
AND SUM(transfer_size)/(1024*1024) >= 5
ORDER BY 6 DESC, 5 DESC;
```

If the staged data remains in S3 after you load it, which is common in data lake architectures, storing this data in a compressed form can reduce your storage costs\. 

**Implementation tips**
+ The ideal object size is 1–128 MB after compression\.
+ You can compress files with gzip, lzop, or bzip2 format\.

## Isolate multiple active databases<a name="isolate-active-dbs-recommendation"></a>

As a best practice, we recommend isolating databases in Amazon Redshift from one another\. Queries run in a specific database and can't access data from any other database on the cluster\. However, the queries that you run in all databases of a cluster share the same underlying cluster storage space and compute resources\. When a single cluster contains multiple active databases, their workloads are usually unrelated\.

**Analysis**

The Advisor analysis reviews all databases on the cluster for active workloads running at the same time\. If there are active workloads running at the same time, Advisor generates a recommendation to consider migrating databases to separate Amazon Redshift clusters\.

**Recommendation**

Consider moving each actively queried database to a separate dedicated cluster\. Using a separate cluster can reduce resource contention and improve query performance\. It can do so because it enables you to set the size for each cluster for the storage, cost, and performance needs of each workload\. Also, unrelated workloads often benefit from different workload management configurations\.

To identify which databases are actively used, you can run this SQL command as a superuser\.

```
SELECT database,
  COUNT(*) as num_queries,
  AVG(DATEDIFF(sec,starttime,endtime)) avg_duration,
  MIN(starttime) as oldest_ts,
  MAX(endtime) as latest_ts
FROM stl_query
WHERE userid > 1
GROUP BY database;
```

**Implementation tips**
+ Because a user must connect to each database specifically, and queries can only access a single database, moving databases to separate clusters has minimal impact for users\.
+ One option to move a database is to take the following steps: 

  1. Temporarily restore a snapshot of the current cluster to a cluster of the same size\.

  1. Delete all databases from the new cluster except the target database to be moved\.

  1. Resize the cluster to an appropriate node type and count for the database's workload\.

## Reallocate workload management \(WLM\) memory<a name="reallocate-wlm-recommendation"></a>

Amazon Redshift routes user queries to [Implementing manual WLM](cm-c-defining-query-queues.md) for processing\. Workload management \(WLM\) defines how those queries are routed to the queues\. Amazon Redshift allocates each queue a portion of the cluster's available memory\. A queue's memory is divided among the queue's query slots\. 

When a queue is configured with more slots than the workload requires, the memory allocated to these unused slots goes underutilized\. Reducing the configured slots to match the peak workload requirements redistributes the underutilized memory to active slots, and can result in improved query performance\. 

**Analysis**

The Advisor analysis reviews workload concurrency requirements to identify query queues with unused slots\. Advisor generates a recommendation to reduce the number of slots in a queue when it finds the following:
+ A queue with slots that are completely inactive throughout the analysis
+ A queue with more than four slots that had at least two inactive slots throughout the analysis

**Recommendation**

Reducing the configured slots to match peak workload requirements redistributes underutilized memory to active slots\. Consider reducing the configured slot count for queues where the slots have never been fully utilized\. To identify these queues, you can compare the peak hourly slot requirements for each queue by running the following SQL command as a superuser\.

```
WITH
 generate_dt_series AS (select sysdate - (n * interval '5 second') as dt from (select row_number() over () as n from stl_scan limit 17280)),
 apex AS (
     SELECT iq.dt, iq.service_class, iq.num_query_tasks, count(iq.slot_count) as service_class_queries, sum(iq.slot_count) as service_class_slots
     FROM
         (select gds.dt, wq.service_class, wscc.num_query_tasks, wq.slot_count
         FROM stl_wlm_query wq
         JOIN stv_wlm_service_class_config wscc ON (wscc.service_class = wq.service_class AND wscc.service_class > 5)
         JOIN generate_dt_series gds ON (wq.service_class_start_time <= gds.dt AND wq.service_class_end_time > gds.dt)
         WHERE wq.userid > 1 AND wq.service_class > 5) iq
     GROUP BY iq.dt, iq.service_class, iq.num_query_tasks),
     maxes as (SELECT apex.service_class, trunc(apex.dt) as d, date_part(h,apex.dt) as dt_h, max(service_class_slots) max_service_class_slots
                     from apex group by apex.service_class, apex.dt, date_part(h,apex.dt))
SELECT apex.service_class - 5 AS queue, apex.service_class, apex.num_query_tasks AS max_wlm_concurrency, maxes.d AS day, maxes.dt_h || ':00 - ' || maxes.dt_h || ':59' as hour, MAX(apex.service_class_slots) as max_service_class_slots
FROM apex
JOIN maxes ON (apex.service_class = maxes.service_class AND apex.service_class_slots = maxes.max_service_class_slots)
GROUP BY  apex.service_class, apex.num_query_tasks, maxes.d, maxes.dt_h
ORDER BY apex.service_class, maxes.d, maxes.dt_h;
```

The `max_service_class_slots` column represents the maximum number of WLM query slots in the query queue for that hour\. If underutilized queues exist, implement the slot reduction optimization by [modifying a parameter group](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-parameter-groups-console.html#parameter-group-modify), as described in the *Amazon Redshift Cluster Management Guide\.*

**Implementation tips**
+ If your workload is highly variable in volume, make sure that the analysis captured a peak utilization period\. If it didn't, run the preceding SQL repeatedly to monitor peak concurrency requirements\. 
+ For more details on interpreting the query results from the preceding SQL code, see the [wlm\_apex\_hourly\.sql script](https://github.com/awslabs/amazon-redshift-utils/blob/master/src/AdminScripts/wlm_apex_hourly.sql) on GitHub\.

## Skip compression analysis during COPY<a name="skip-compression-analysis-recommendation"></a>

When you load data into an empty table with compression encoding declared with the COPY command, Amazon Redshift applies storage compression\. This optimization ensures that data in your cluster is stored efficiently even when loaded by end users\. The analysis required to apply compression can require significant time\.

**Analysis**

The Advisor analysis checks for COPY operations that were delayed by automatic compression analysis\. The analysis determines the compression encodings by sampling the data while it's being loaded\. This sampling is similar to that performed by the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command\. 

When you load data as part of a structured process, such as in an overnight extract, transform, load \(ETL\) batch, you can define the compression beforehand\. You can also optimize your table definitions to permanently skip this phase without any negative impacts\.

**Recommendation**

To improve COPY responsiveness by skipping the compression analysis phase, implement either of the following two options:
+ Use the column `ENCODE` parameter when creating any tables that you load using the COPY command\.
+ Disable compression altogether by supplying the `COMPUPDATE OFF` parameter in the COPY command\.

The best solution is generally to use column encoding during table creation, because this approach also maintains the benefit of storing compressed data on disk\. You can use the ANALYZE COMPRESSION command to suggest compression encodings, but you must recreate the table to apply these encodings\. To automate this process, you can use the AWS [ColumnEncodingUtility](https://github.com/awslabs/amazon-redshift-utils/tree/master/src/ColumnEncodingUtility), found on GitHub\. 

To identify recent COPY operations that triggered automatic compression analysis, run the following SQL command\.

```
  WITH xids AS (
    SELECT xid FROM stl_query WHERE userid>1 AND aborted=0 
    AND querytxt = 'analyze compression phase 1' GROUP BY xid
    INTERSECT SELECT xid FROM stl_commit_stats WHERE node=-1)
SELECT a.userid, a.query, a.xid, a.starttime, b.complyze_sec, 
    a.copy_sec, a.copy_sql
FROM (SELECT q.userid, q.query, q.xid, date_trunc('s',q.starttime) 
    starttime, substring(querytxt,1,100) as copy_sql, 
    ROUND(datediff(ms,starttime,endtime)::numeric / 1000.0, 2) copy_sec
    FROM stl_query q JOIN xids USING (xid)
    WHERE (querytxt ilike 'copy %from%' OR querytxt ilike '% copy %from%') 
    AND querytxt not like 'COPY ANALYZE %') a
LEFT JOIN (SELECT xid, 
    ROUND(sum(datediff(ms,starttime,endtime))::numeric / 1000.0,2) complyze_sec 
    FROM stl_query q JOIN xids USING (xid)
    WHERE (querytxt like 'COPY ANALYZE %' 
    OR querytxt like 'analyze compression phase %') 
    GROUP BY xid ) b ON a.xid = b.xid
WHERE b.complyze_sec IS NOT NULL ORDER BY a.copy_sql, a.starttime;
```

**Implementation tips**
+ Ensure that all tables of significant size created during your ETL processes \(for example, staging tables and temporary tables\) declare a compression encoding for all columns except the first sort key\.
+ Estimate the expected lifetime size of the table being loaded for each of the COPY commands identified by the SQL command preceding\. If you are confident that the table will remain extremely small, disable compression altogether with the `COMPUPDATE OFF` parameter\. Otherwise, create the table with explicit compression before loading it with the COPY command\.

## Split Amazon S3 objects loaded by COPY<a name="split-s3-objects-recommendation"></a>

The COPY command takes advantage of the massively parallel processing \(MPP\) architecture in Amazon Redshift to read and load data from files on Amazon S3\. The COPY command loads the data in parallel from multiple files, dividing the workload among the nodes in your cluster\. To achieve optimal throughput, we strongly recommend that you divide your data into multiple files to take advantage of parallel processing\. 

**Analysis**

The Advisor analysis identifies COPY commands that load large datasets contained in a small number of files staged in S3\. Long\-running COPY commands that load large datasets from a few files often have an opportunity for considerable performance improvement\. When Advisor identifies that these COPY commands are taking a significant amount of time, it creates a recommendation to increase parallelism by splitting the data into additional files in S3\. 

**Recommendation**

In this case, we recommend the following actions, listed in priority order:

1. Optimize COPY commands that load fewer files than the number of cluster nodes\.

1. Optimize COPY commands that load fewer files than the number of cluster slices\. 

1. Optimize COPY commands where the number of files is not a multiple of the number of cluster slices\.

Certain COPY commands load a significant amount of data or run for a significant duration\. For these commands, we recommend that you load a number of data objects from S3 that is equivalent to a multiple of the number of slices in the cluster\. To identify how many S3 objects each COPY command has loaded, run the following SQL code as a superuser\. 

```
SELECT
    query, COUNT(*) num_files,
    ROUND(MAX(wq.total_exec_time/1000000.0),2) execution_secs,
    ROUND(SUM(transfer_size)/(1024.0*1024.0),2) total_mb,
    SUBSTRING(querytxt,1,60) copy_sql
FROM stl_s3client s
JOIN stl_query q USING (query)
JOIN stl_wlm_query wq USING (query)
WHERE s.userid>1 AND http_method = 'GET'
    AND POSITION('COPY ANALYZE' IN querytxt) = 0
    AND aborted = 0 AND final_state='Completed'
GROUP BY query, querytxt
HAVING (SUM(transfer_size)/(1024*1024))/COUNT(*) >= 2
ORDER BY CASE
WHEN COUNT(*) < (SELECT max(node)+1 FROM stv_slices) THEN 1
WHEN COUNT(*) < (SELECT COUNT(*) FROM stv_slices WHERE node=0) THEN 2
ELSE 2+((COUNT(*) % (SELECT COUNT(*) FROM stv_slices))/(SELECT COUNT(*)::DECIMAL FROM stv_slices))
END, (SUM(transfer_size)/(1024.0*1024.0))/COUNT(*) DESC;
```

**Implementation tips**
+ The number of slices in a node depends on the node size of the cluster\. For more information about the number of slices in the various node types, see [Clusters and Nodes in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide\.* 
+ You can load multiple files by specifying a common prefix, or prefix key, for the set, or by explicitly listing the files in a manifest file\. For more information about loading files, see [Splitting your data into multiple files](t_splitting-data-files.md)\.
+ Amazon Redshift doesn't take file size into account when dividing the workload\. Split your load data files so that the files are about equal size, between 1 MB and 1 GB after compression\. For optimum parallelism, the ideal size is between 1 MB and 125 MB after compression\. 

## Update table statistics<a name="update-table-statistics-recommendation"></a>

Amazon Redshift uses a cost\-based query optimizer to choose the optimum execution plan for queries\. The cost estimates are based on table statistics gathered using the ANALYZE command\. When statistics are out of date or missing, the database might choose a less efficient plan for query execution, especially for complex queries\. Maintaining current statistics helps complex queries run in the shortest possible time\. 

**Analysis**

The Advisor analysis tracks tables whose statistics are out\-of\-date or missing\. It reviews table access metadata associated with complex queries\. If tables that are frequently accessed with complex patterns are missing statistics, Advisor creates a **critical** recommendation to run ANALYZE\. If tables that are frequently accessed with complex patterns have out\-of\-date statistics, Advisor creates a **suggested** recommendation to run ANALYZE\.

**Recommendation**

Whenever table content changes significantly, update statistics with ANALYZE\. We recommend running ANALYZE whenever a significant number of new data rows are loaded into an existing table with COPY or INSERT commands\. We also recommend running ANALYZE whenever a significant number of rows are modified using UPDATE or DELETE commands\. To identify tables with missing or out\-of\-date statistics, run the following SQL command as a superuser\. The results are ordered from largest to smallest table\. 

To identify tables with missing or out\-of\-date statistics, run the following SQL command as a superuser\. The results are ordered from largest to smallest table\.

```
SELECT
   ti.schema||'.'||ti."table" tablename,
   ti.size table_size_mb,
   ti.stats_off statistics_accuracy
 FROM svv_table_info ti
 WHERE ti.stats_off > 5.00
 ORDER BY ti.size DESC;
```

**Implementation tips**

The default ANALYZE threshold is 10 percent\. This default means that the ANALYZE command skips a given table if fewer than 10 percent of the table's rows have changed since the last ANALYZE\. As a result, you might choose to issue ANALYZE commands at the end of each ETL process\. Taking this approach means that ANALYZE is often skipped but also ensures that ANALYZE runs when needed\.

ANALYZE statistics have the most impact for columns that are used in joins \(for example, `JOIN tbl_a ON col_b`\) or as predicates \(for example, `WHERE col_b = 'xyz'`\)\. By default, ANALYZE collects statistics for all columns in the table specified\. If needed, you can reduce the time required to run ANALYZE by running ANALYZE only for the columns where it has the most impact\. You can run the following SQL command to identify columns used as predicates\. You can also let Amazon Redshift choose which columns to analyze by specifying `ANALYZE PREDICATE COLUMNS`\. 

```
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

For more information, see [Analyzing tables](t_Analyzing_tables.md)\.

## Enable short query acceleration<a name="enable-sqa-recommendation"></a>

Short query acceleration \(SQA\) prioritizes selected short\-running queries ahead of longer\-running queries\. SQA executes short\-running queries in a dedicated space, so that SQA queries aren't forced to wait in queues behind longer queries\. SQA only prioritizes queries that are short\-running and are in a user\-defined queue\. With SQA, short\-running queries begin running more quickly and users see results sooner\. 

If you enable SQA, you can reduce or eliminate workload management \(WLM\) queues that are dedicated to running short queries\. In addition, long\-running queries don't need to contend with short queries for slots in a queue, so you can configure your WLM queues to use fewer query slots\. When you use lower concurrency, query throughput is increased and overall system performance is improved for most workloads\. For more information, see [Working with short query acceleration](wlm-short-query-acceleration.md)\. 

**Analysis**

Advisor checks for workload patterns and reports the number of recent queries where SQA would reduce latency and the daily queue time for SQA\-eligible queries\.

**Recommendation**

Modify the WLM configuration to enable SQA\. Amazon Redshift uses a machine learning algorithm to analyze each eligible query\. Predictions improve as SQA learns from your query patterns\. For more information, see [Configuring Workload Management](https://docs.aws.amazon.com/redshift/latest/mgmt/workload-mgmt-config.html)\. 

When you enable SQA, WLM sets the maximum run time for short queries to dynamic by default\. We recommend keeping the dynamic setting for SQA maximum run time\. 

**Implementation tips**

To check whether SQA is enabled, run the following query\. If the query returns a row, then SQA is enabled\.

```
select * from stv_wlm_service_class_config 
where service_class = 14;
```

For more information, see [Monitoring SQA](wlm-short-query-acceleration.md#wlm-monitoring-sqa)\. 

## Replace single\-column interleaved sort keys<a name="single-column-interleaved-sort-recommendation"></a>

Some tables use an interleaved sort key on a single column\. In general, such a table is less efficient and consumes more resources than a table that uses a compound sort key on a single column\.

Interleaved sorting improves performance in certain cases where multiple columns are used by different queries for filtering\. Using an interleaved sort key on a single column is effective only in a particular case\. That case is when queries often filter on CHAR or VARCHAR column values that have a long common prefix in the first 8 bytes\. For example, URL strings are often prefixed with "`https://`"\. For single\-column keys, a compound sort is better than an interleaved sort for any other filtering operations\. A compound sort speeds up joins, GROUP BY and ORDER BY operations, and window functions that use PARTITION BY and ORDER BY on the sorted column\. An interleaved sort doesn't benefit any of those operations\. For more information, see [Choosing sort keys](t_Sorting_data.md)\. 

Using compound sort significantly reduces maintenance overhead\. Tables with compound sort keys don't need the expensive VACUUM REINDEX operations that are necessary for interleaved sorts\. In practice, compound sort keys are more effective than interleaved sort keys for the vast majority of Amazon Redshift workloads\. 

**Analysis**

Advisor tracks tables that use an interleaved sort key on a single column\. 

**Recommendation**

If a table uses interleaved sorting on a single column, recreate the table to use a compound sort key\. When you create new tables, use a compound sort key for single\-column sorts\. To find interleaved tables that use a single\-column sort key, run the following command\. 

```
SELECT schema AS schemaname, "table" AS tablename 
FROM svv_table_info 
WHERE table_id IN (
    SELECT attrelid 
    FROM pg_attribute
    WHERE attrelid IN (
        SELECT attrelid
        FROM pg_attribute
        WHERE attsortkeyord <> 0
        GROUP BY attrelid
        HAVING MAX(attsortkeyord) = -1
    )
    AND NOT (atttypid IN (1042, 1043) AND atttypmod > 12)
    AND attsortkeyord = -1);
```

For additional information about choosing the best sort style, see the AWS Big Data Blog post [Amazon Redshift Engineering's Advanced Table Design Playbook: Compound and Interleaved Sort Keys](https://aws.amazon.com/blogs/big-data/amazon-redshift-engineerings-advanced-table-design-playbook-compound-and-interleaved-sort-keys/)\. 

## Alter distribution keys on tables<a name="alter-diststyle-distkey-recommendation"></a>

Amazon Redshift distributes table rows throughout the cluster according to the table distribution style\. Tables with KEY distribution require a column as the distribution key \(DISTKEY\)\. A table row is assigned to a node slice of a cluster based on its DISTKEY column value\. 

An appropriate DISTKEY places a similar number of rows on each node slice and is frequently referenced in join conditions\. An optimized join occurs when tables are joined on their DISTKEY columns, accelerating query performance\.

**Analysis**

Advisor analyzes your cluster’s workload to identify the most appropriate distribution key for the tables that can significantly benefit from a KEY distribution style\. 

**Recommendation**

Advisor provides [ALTER TABLE](r_ALTER_TABLE.md) statements that alter the DISTSTYLE and DISTKEY of a table based on its analysis\. To realize a significant performance benefit, make sure to implement all SQL statements within a recommendation group\. 

Redistributing a large table with ALTER TABLE consumes cluster resources and requires temporary table locks at various times\. Implement each recommendation group when other cluster workload is light\. For more details on optimizing table distribution properties, see the [Amazon Redshift Engineering's Advanced Table Design Playbook: Distribution Styles and Distribution Keys](https://aws.amazon.com/blogs/big-data/amazon-redshift-engineerings-advanced-table-design-playbook-distribution-styles-and-distribution-keys/)\. 

For more information about ALTER DISTSYLE and DISTKEY, see [ALTER TABLE](r_ALTER_TABLE.md)\. 

**Note**  
If you don't see a recommendation, that doesn't necessarily mean that the current distribution styles are the most appropriate\. Advisor doesn't provide recommendations when there isn't enough data or the expected benefit of redistribution is small\.   
Advisor recommendations apply to a particular table and don't necessarily apply to a table that contains a column with the same name\. Tables that share a column name can have different characteristics for those columns unless data inside the tables is the same\.   
If you see recommendations for staging tables that are created or dropped by ETL jobs, modify your ETL processes to use the Advisor recommended distribution keys\. 

## Alter sort keys on tables<a name="alter-sortkey-recommendation"></a>

Amazon Redshift sorts table rows according to the table [sort key](t_Sorting_data.md)\. The sorting of table rows is based on the sort key column values\. 

Sorting a table on an appropriate sort key can accelerate performance of queries, especially those with range\-restricted predicates, by requiring fewer table blocks to be read from disk\. 

**Analysis**

Advisor analyzes your cluster’s workload over several days to identify a beneficial sort key for your tables\. 

**Recommendation**

Advisor provides ALTER TABLE statements that alter the sort key of a table based on its analysis\.

When sorting a large table with the ALTER TABLE, cluster resources are consumed and table locks are required at various times\. Implement each recommendation when a cluster's workload is moderate\. More details on optimizing table sort key configurations can be found in the [Amazon Redshift Engineering's Advanced Table Design Playbook: Compound and Interleaved Sort Keys](https://aws.amazon.com/blogs/big-data/amazon-redshift-engineerings-advanced-table-design-playbook-compound-and-interleaved-sort-keys/)\. 

For more information about ALTER SORTKEY, see [ALTER TABLE](r_ALTER_TABLE.md)\. 

**Note**  
If you don't see a recommendation for a table, that doesn't necessarily mean that the current configuration is the best\. Advisor doesn't provide recommendations when there isn't enough data or the expected benefit of sorting is small\.   
Advisor recommendations apply to a particular table and don’t necessarily apply to a table that contains a column with the same name and data type\. Tables that share column names can have different recommendations based on the data in the tables and the workload\. 