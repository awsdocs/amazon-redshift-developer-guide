# Vacuuming Tables<a name="t_Reclaiming_storage_space202"></a>

To clean up tables after a load or a series of incremental updates, you need to run the [VACUUM](r_VACUUM_command.md) command, either against the entire database or against individual tables\.

**Note**  
Only the table owner or a superuser can effectively vacuum a table\. If you do not have owner or superuser privileges for a table, a VACUUM that specifies a single table will fail\. If you run a VACUUM of the entire database, without specifying a table name, the operation completes successfully but has no effect on tables for which you do not have owner or superuser privileges\. For this reason, and because vacuuming the entire database is potentially an expensive operation, we recommend vacuuming individual tables as needed\. 

When you perform a delete, the rows are marked for deletion, but not removed\. Amazon Redshift automatically runs a VACUUM DELETE operation in the background based on the number of deleted rows in database tables\. Amazon Redshift schedules the VACUUM DELETE to run during periods of reduced load and pauses the operation during periods of high load\. Automatic VACUUM DELETE is not active in all AWS Regions\.

For tables with a sort key, the VACUUM command ensures that new data in tables is fully sorted on disk\. When data is initially loaded into a table that has a sort key, the data is sorted according to the SORTKEY specification in the [CREATE TABLE](r_CREATE_TABLE_NEW.md) statement\. However, when you update the table, using COPY, INSERT, or UPDATE statements, new rows are stored in a separate unsorted region on disk, then sorted on demand for queries as required\. If large numbers of rows remain unsorted on disk, query performance might be degraded for operations that rely on sorted data, such as range\-restricted scans or merge joins\. The VACUUM command merges new rows with existing sorted rows, so range\-restricted scans are more efficient and the execution engine doesn't need to sort rows on demand during query execution\. 

When a table is sorted using an interleaved sort key, Amazon Redshift analyzes the distribution of values in the sort key columns to determine the optimal sort strategy\. Over time, that distribution can change, or skew, which might degrade performance\. Run a [VACUUM REINDEX](r_VACUUM_command.md#vacuum-reindex) to re\-analyze the sort key distribution and restore performance\. For more information, see [Interleaved Sort Key](t_Sorting_data.md#t_Sorting_data-interleaved)\.

**Topics**
+ [VACUUM Frequency](#vacuum-frequency)
+ [Sort Stage and Merge Stage](#vacuum-stages)
+ [Vacuum Threshold](#vacuum-sort-threshold)
+ [Vacuum Types](#vacuum-types)
+ [Managing Vacuum Times](vacuum-managing-vacuum-times.md)

## VACUUM Frequency<a name="vacuum-frequency"></a>

You should vacuum as often as you need to in order to maintain consistent query performance\. Consider these factors when determining how often to run your VACUUM command\. 
+ Run VACUUM during time periods when you expect minimal activity on the cluster, such as evenings or during designated database administration windows\. 
+ A large unsorted region results in longer vacuum times\. If you delay vacuuming, the vacuum will take longer because more data has to be reorganized\. 
+ VACUUM is an I/O intensive operation, so the longer it takes for your vacuum to complete, the more impact it will have on concurrent queries and other database operations running on your cluster\. 
+ VACUUM takes longer for tables that use interleaved sorting\. To evaluate whether interleaved tables need to be resorted, query the [SVV\_INTERLEAVED\_COLUMNS](r_SVV_INTERLEAVED_COLUMNS.md) view\.

## Sort Stage and Merge Stage<a name="vacuum-stages"></a>

Amazon Redshift performs a vacuum operation in two stages: first, it sorts the rows in the unsorted region, then, if necessary, it merges the newly sorted rows at the end of the table with the existing rows\. When vacuuming a large table, the vacuum operation proceeds in a series of steps consisting of incremental sorts followed by merges\. If the operation fails or if Amazon Redshift goes off line during the vacuum, the partially vacuumed table or database will be in a consistent state, but you will need to manually restart the vacuum operation\. Incremental sorts are lost, but merged rows that were committed before the failure do not need to be vacuumed again\. If the unsorted region is large, the lost time might be significant\. For more information about the sort and merge stages, see [Managing the Volume of Merged Rows](vacuum-managing-volume-of-unmerged-rows.md)\.

Users can access tables while they are being vacuumed\. You can perform queries and write operations while a table is being vacuumed, but when DML and a vacuum run concurrently, both might take longer\. If you execute UPDATE and DELETE statements during a vacuum, system performance might be reduced\. Incremental merges temporarily block concurrent UPDATE and DELETE operations, and UPDATE and DELETE operations in turn temporarily block incremental merge steps on the affected tables\. DDL operations, such as ALTER TABLE, are blocked until the vacuum operation finishes with the table\.

## Vacuum Threshold<a name="vacuum-sort-threshold"></a>

By default, VACUUM skips the sort phase for any table where more than 95 percent of the table's rows are already sorted\. Skipping the sort phase can significantly improve VACUUM performance\. To change the default sort threshold for a single table, include the table name and the TO *threshold* PERCENT parameter when you run the VACUUM command\. 

## Vacuum Types<a name="vacuum-types"></a>

You can run a full vacuum, a delete only vacuum, a sort only vacuum, or a reindex with full vacuum\.
+  **VACUUM FULL** 

  VACUUM FULL resorts rows and reclaims space from deleted rows\. Amazon Redshift automatically performs VACUUM DELETE ONLY operations in the background, so for most applications, VACUUM FULL and VACUUM SORT ONLY are equivalent\. VACUUM FULL is the same as VACUUM\. Full vacuum is the default vacuum operation\.
+  **VACUUM DELETE ONLY** 

  A DELETE ONLY vacuum is the same as a full vacuum except that it skips the sort\. Amazon Redshift automatically performs a DELETE ONLY vacuum in the background, so you rarely, if ever, need to run a DELETE ONLY vacuum\. 
+  **VACUUM SORT ONLY** 

  A SORT ONLY doesn't reclaim disk space\. In most cases there is little benefit compared to a full vacuum\.
+  **VACUUM REINDEX** 

  Use VACUUM REINDEX for tables that use interleaved sort keys\. 

  When you initially load an empty interleaved table using COPY or CREATE TABLE AS, Amazon Redshift automatically builds the interleaved index\. If you initially load an interleaved table using INSERT, you need to run VACUUM REINDEX afterwards to initialize the interleaved index\.

  REINDEX reanalyzes the distribution of the values in the table's sort key columns, then performs a full VACUUM operation\. VACUUM REINDEX takes significantly longer than VACUUM FULL because it needs to take an extra analysis pass over the data, and because merging in new interleaved data can involve touching all the data blocks\.

  If a VACUUM REINDEX operation terminates before it completes, the next VACUUM resumes the reindex operation before performing the vacuum\.