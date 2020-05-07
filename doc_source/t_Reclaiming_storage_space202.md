# Vacuuming tables<a name="t_Reclaiming_storage_space202"></a>

Amazon Redshift can automatically sort and perform a VACUUM DELETE operation on tables in the background\. To clean up tables after a load or a series of incremental updates, you can also run the [VACUUM](r_VACUUM_command.md) command, either against the entire database or against individual tables\.

**Note**  
Only the table owner or a superuser can effectively vacuum a table\. If you don't have owner or superuser privileges for a table, a VACUUM operation that specifies a single table fails\. If you run a VACUUM of the entire database without specifying a table name, the operation completes successfully\. However, the operation has no effect on tables for which you don't have owner or superuser privileges\.   
For this reason, we recommend vacuuming individual tables as needed\. We also recommend this approach because vacuuming the entire database is potentially an expensive operation, 

## Automatic table sort<a name="automatic-table-sort"></a>

Amazon Redshift automatically sorts data in the background to maintain table data in the order of its sort key\. Amazon Redshift keeps track of your scan queries to determine which sections of the table will benefit from sorting\. 

Depending on the load on the system, Amazon Redshift automatically initiates the sort\. This automatic sort lessens the need to run the VACUUM command to keep data in sort key order\. If you need data fully sorted in sort key order, for example after a large data load, then you can still manually run the VACUUM command\. To determine whether your table will benefit by running VACUUM SORT, monitor the `vacuum_sort_benefit` column in [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)\. 

Amazon Redshift tracks scan queries that use the sort key on each table\. Amazon Redshift estimates the maximum percentage of improvement in scanning and filtering of data for each table \(if the table was fully sorted\)\. This estimate is visible in the `vacuum_sort_benefit` column in [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)\. You can use this column, along with the `unsorted` column, to determine when queries can benefit from manually running VACUUM SORT on a table\. The `unsorted` column reflects the physical sort order of a table\.  The `vacuum_sort_benefit` column specifies the impact of sorting a table by manually running VACUUM SORT\.

For example, consider the following query:

```
select "table", unsorted,vacuum_sort_benefit from svv_table_info order by 1;
```

```
 table | unsorted | vacuum_sort_benefit 
-------+----------+---------------------
 sales |    85.71 |                5.00
 event |    45.24 |               67.00
```

For the table “sales”, even though the table is \~86% physically unsorted, the query performance impact from the table being 86% unsorted is only 5%\. This might be either because only a small portion of the table is accessed by queries, or very few queries accessed the table\. For the table “event”, the table is \~45% physically unsorted\. But the query performance impact of 67% indicates that either a larger portion of the table was accessed by queries, or the number of queries accessing the table was large\. The table "event" can potentially benefit from running VACUUM SORT\.

## Automatic vacuum delete<a name="automatic-table-delete"></a>

When you perform a delete, the rows are marked for deletion, but not removed\. Amazon Redshift automatically runs a VACUUM DELETE operation in the background based on the number of deleted rows in database tables\. Amazon Redshift schedules the VACUUM DELETE to run during periods of reduced load and pauses the operation during periods of high load\. 

**Topics**
+ [Automatic table sort](#automatic-table-sort)
+ [Automatic vacuum delete](#automatic-table-delete)
+ [VACUUM frequency](#vacuum-frequency)
+ [Sort stage and merge stage](#vacuum-stages)
+ [Vacuum threshold](#vacuum-sort-threshold)
+ [Vacuum types](#vacuum-types)
+ [Managing vacuum times](vacuum-managing-vacuum-times.md)

## VACUUM frequency<a name="vacuum-frequency"></a>

You should vacuum as often as you need to in order to maintain consistent query performance\. Consider these factors when determining how often to run your VACUUM command\. 
+ Run VACUUM during time periods when you expect minimal activity on the cluster, such as evenings or during designated database administration windows\. 
+ A large unsorted region results in longer vacuum times\. If you delay vacuuming, the vacuum will take longer because more data has to be reorganized\. 
+ VACUUM is an I/O intensive operation, so the longer it takes for your vacuum to complete, the more impact it will have on concurrent queries and other database operations running on your cluster\. 
+ VACUUM takes longer for tables that use interleaved sorting\. To evaluate whether interleaved tables need to be re\-sorted, query the [SVV\_INTERLEAVED\_COLUMNS](r_SVV_INTERLEAVED_COLUMNS.md) view\.

## Sort stage and merge stage<a name="vacuum-stages"></a>

Amazon Redshift performs a vacuum operation in two stages: first, it sorts the rows in the unsorted region, then, if necessary, it merges the newly sorted rows at the end of the table with the existing rows\. When vacuuming a large table, the vacuum operation proceeds in a series of steps consisting of incremental sorts followed by merges\. If the operation fails or if Amazon Redshift goes off line during the vacuum, the partially vacuumed table or database will be in a consistent state, but you will need to manually restart the vacuum operation\. Incremental sorts are lost, but merged rows that were committed before the failure do not need to be vacuumed again\. If the unsorted region is large, the lost time might be significant\. For more information about the sort and merge stages, see [Managing the volume of merged rows](vacuum-managing-volume-of-unmerged-rows.md)\.

Users can access tables while they are being vacuumed\. You can perform queries and write operations while a table is being vacuumed, but when DML and a vacuum run concurrently, both might take longer\. If you execute UPDATE and DELETE statements during a vacuum, system performance might be reduced\. Incremental merges temporarily block concurrent UPDATE and DELETE operations, and UPDATE and DELETE operations in turn temporarily block incremental merge steps on the affected tables\. DDL operations, such as ALTER TABLE, are blocked until the vacuum operation finishes with the table\.

## Vacuum threshold<a name="vacuum-sort-threshold"></a>

By default, VACUUM skips the sort phase for any table where more than 95 percent of the table's rows are already sorted\. Skipping the sort phase can significantly improve VACUUM performance\. To change the default sort threshold for a single table, include the table name and the TO *threshold* PERCENT parameter when you run the VACUUM command\. 

## Vacuum types<a name="vacuum-types"></a>

You can run a full vacuum, a delete only vacuum, a sort only vacuum, or a reindex with full vacuum\.
+  **VACUUM FULL** 

  VACUUM FULL re\-sorts rows and reclaims space from deleted rows\. Amazon Redshift automatically performs VACUUM DELETE ONLY operations in the background, so for most applications, VACUUM FULL and VACUUM SORT ONLY are equivalent\. VACUUM FULL is the same as VACUUM\. Full vacuum is the default vacuum operation\.
+  **VACUUM DELETE ONLY** 

  A DELETE ONLY vacuum is the same as a full vacuum except that it skips the sort\. Amazon Redshift automatically performs a DELETE ONLY vacuum in the background, so you rarely, if ever, need to run a DELETE ONLY vacuum\. 
+  **VACUUM SORT ONLY** 

  A SORT ONLY doesn't reclaim disk space\. In most cases there is little benefit compared to a full vacuum\.
+  **VACUUM REINDEX** 

  Use VACUUM REINDEX for tables that use interleaved sort keys\. For more information about interleaved sort keys, see [Interleaved sort key](t_Sorting_data.md#t_Sorting_data-interleaved)\. 

  When you initially load an empty interleaved table using COPY or CREATE TABLE AS, Amazon Redshift automatically builds the interleaved index\. If you initially load an interleaved table using INSERT, you need to run VACUUM REINDEX afterwards to initialize the interleaved index\.

  REINDEX reanalyzes the distribution of the values in the table's sort key columns, then performs a full VACUUM operation\. VACUUM REINDEX takes significantly longer than VACUUM FULL because it needs to take an extra analysis pass over the data, and because merging in new interleaved data can involve touching all the data blocks\.

  If a VACUUM REINDEX operation terminates before it completes, the next VACUUM resumes the reindex operation before performing the vacuum\. 