# VACUUM<a name="r_VACUUM_command"></a>

Re\-sorts rows and reclaims space in either a specified table or all tables in the current database\.

**Note**  
Only the table owner or a superuser can effectively vacuum a table\. If VACUUM is run without the necessary table privileges, the operation completes successfully but has no effect\. 

Amazon Redshift automatically sorts data and runs VACUUM DELETE in the background\. This lessens the need to run the VACUUM command\. For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\. 

By default, VACUUM skips the sort phase for any table where more than 95 percent of the table's rows are already sorted\. Skipping the sort phase can significantly improve VACUUM performance\. To change the default sort or delete threshold for a single table, include the table name and the TO *threshold* PERCENT parameter when you run VACUUM\. 

Users can access tables while they are being vacuumed\. You can perform queries and write operations while a table is being vacuumed, but when data manipulation language \(DML\) commands and a vacuum run concurrently, both might take longer\. If you execute UPDATE and DELETE statements during a vacuum, system performance might be reduced\. VACUUM DELETE temporarily blocks update and delete operations\. 

Amazon Redshift automatically performs a DELETE ONLY vacuum in the background\. Automatic vacuum operation pauses when users run data definition language \(DDL\) operations, such as ALTER TABLE\.

**Note**  
The Amazon Redshift VACUUM command syntax and behavior are substantially different from the PostgreSQL VACUUM operation\. For example, the default VACUUM operation in Amazon Redshift is VACUUM FULL, which reclaims disk space and re\-sorts all rows\. In contrast, the default VACUUM operation in PostgreSQL simply reclaims space and makes it available for reuse\.

For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\.

## Syntax<a name="r_VACUUM_command-synopsis"></a>

```
VACUUM [ FULL | SORT ONLY | DELETE ONLY | REINDEX ] 
[ [ table_name ] [ TO threshold PERCENT ] [ BOOST ] ]
```

## Parameters<a name="r_VACUUM_command-parameters"></a>

FULL   <a name="vacuum-full"></a>
Sorts the specified table \(or all tables in the current database\) and reclaims disk space occupied by rows that were marked for deletion by previous UPDATE and DELETE operations\. VACUUM FULL is the default\.  
A full vacuum doesn't perform a reindex for interleaved tables\. To reindex interleaved tables followed by a full vacuum, use the [VACUUM REINDEX](#vacuum-reindex) option\.   
By default, VACUUM FULL skips the sort phase for any table that is already at least 95 percent sorted\. If VACUUM is able to skip the sort phase, it performs a DELETE ONLY and reclaims space in the delete phase such that at least 95 percent of the remaining rows aren't marked for deletion\.    
If the sort threshold isn't met \(for example, if 90 percent of rows are sorted\) and VACUUM performs a full sort, then it also performs a complete delete operation, recovering space from 100 percent of deleted rows\.   
You can change the default vacuum threshold only for a single table\. To change the default vacuum threshold for a single table, include the table name and the TO *threshold* PERCENT parameter\. 

SORT ONLY   <a name="vacuum-sort-only"></a>
Sorts the specified table \(or all tables in the current database\) without reclaiming space freed by deleted rows\. This option is useful when reclaiming disk space isn't important but re\-sorting new rows is important\. A SORT ONLY vacuum reduces the elapsed time for vacuum operations when the unsorted region doesn't contain a large number of deleted rows and doesn't span the entire sorted region\. Applications that don't have disk space constraints but do depend on query optimizations associated with keeping table rows sorted can benefit from this kind of vacuum\.  
By default, VACUUM SORT ONLY skips any table that is already at least 95 percent sorted\. To change the default sort threshold for a single table, include the table name and the TO *threshold* PERCENT parameter when you run VACUUM\. 

DELETE ONLY   <a name="vacuum-delete-only"></a>
Amazon Redshift automatically performs a DELETE ONLY vacuum in the background, so you rarely, if ever, need to run a DELETE ONLY vacuum\.  
A VACUUM DELETE reclaims disk space occupied by rows that were marked for deletion by previous UPDATE and DELETE operations, and compacts the table to free up the consumed space\. A DELETE ONLY vacuum operation doesn't sort table data\.   
This option reduces the elapsed time for vacuum operations when reclaiming disk space is important but re\-sorting new rows isn't important\. This option can also be useful when your query performance is already optimal, and re\-sorting rows to optimize query performance isn't a requirement\.  
By default, VACUUM DELETE ONLY reclaims space such that at least 95 percent of the remaining rows aren't marked for deletion\. To change the default delete threshold for a single table, include the table name and the TO *threshold* PERCENT parameter when you run VACUUM\.    
Some operations, such as `ALTER TABLE APPEND`, can cause tables to be fragmented\. When you use the `DELETE ONLY` clause the vacuum operation reclaims space from fragmented tables\. The same threshold value of 95 percent applies to the defragmentation operation\. 

REINDEX *tablename*  <a name="vacuum-reindex"></a>
Analyzes the distribution of the values in interleaved sort key columns, then performs a full VACUUM operation\. If REINDEX is used, a table name is required\.  
VACUUM REINDEX takes significantly longer than VACUUM FULL because it makes an additional pass to analyze the interleaved sort keys\. The sort and merge operation can take longer for interleaved tables because the interleaved sort might need to rearrange more rows than a compound sort\.  
If a VACUUM REINDEX operation terminates before it completes, the next VACUUM resumes the reindex operation before performing the full vacuum operation\.  
VACUUM REINDEX isn't supported with TO *threshold* PERCENT\.  

 *table\_name*   
The name of a table to vacuum\. If you don't specify a table name, the vacuum operation applies to all tables in the current database\. You can specify any permanent or temporary user\-created table\. The command isn't meaningful for other objects, such as views and system tables\.  
 If you include the TO *threshold* PERCENT parameter, a table name is required\.

 TO *threshold* PERCENT   
A clause that specifies the threshold above which VACUUM skips the sort phase and the target threshold for reclaiming space in the delete phase\. The *sort threshold* is the percentage of total rows that are already in sort order for the specified table prior to vacuuming\.  The *delete threshold* is the minimum percentage of total rows not marked for deletion after vacuuming\.   
Because VACUUM re\-sorts the rows only when the percent of sorted rows in a table is less than the sort threshold, Amazon Redshift can often reduce VACUUM times significantly\. Similarly, when VACUUM isn't constrained to reclaim space from 100 percent of rows marked for deletion, it is often able to skip rewriting blocks that contain only a few deleted rows\.  
For example, if you specify 75 for *threshold*, VACUUM skips the sort phase if 75 percent or more of the table's rows are already in sort order\. For the delete phase, VACUUMS sets a target of reclaiming disk space such that at least 75 percent of the table's rows aren't marked for deletion following the vacuum\. The *threshold* value must be an integer between 0 and 100\. The default is 95\. If you specify a value of 100, VACUUM always sorts the table unless it's already fully sorted and reclaims space from all rows marked for deletion\. If you specify a value of 0, VACUUM never sorts the table and never reclaims space\.  
If you include the TO *threshold* PERCENT parameter, you must also specify a table name\. If a table name is omitted, VACUUM fails\.   
You can't use the TO *threshold* PERCENT parameter with REINDEX\. 

BOOST  
Runs the VACUUM command with additional resources, such as memory and disk space, as they're available\. With the BOOST option, VACUUM operates in one window and blocks concurrent deletes and updates for the duration of the VACUUM operation\. Running with the BOOST option contends for system resources, which might affect query performance\. Run the VACUUM BOOST when the load on the system is light, such as during maintenance operations\.   
Consider the following when using the BOOST option:  
+ When BOOST is specified, the *table\_name* value is required\. 
+ BOOST isn't supported with REINDEX\. 
+ BOOST is ignored with DELETE ONLY\. 

## Usage notes<a name="r_VACUUM_usage_notes"></a>

For most Amazon Redshift applications, a full vacuum is recommended\. For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\.

Before running a vacuum operation, note the following behavior: 
+ You can't run VACUUM within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 
+ You can run only one VACUUM command on a cluster at any given time\. If you attempt to run multiple vacuum operations concurrently, Amazon Redshift returns an error\.
+ Some amount of table growth might occur when tables are vacuumed\. This behavior is expected when there are no deleted rows to reclaim or the new sort order of the table results in a lower ratio of data compression\.
+ During vacuum operations, some degree of query performance degradation is expected\. Normal performance resumes as soon as the vacuum operation is complete\.
+ Concurrent write operations proceed during vacuum operations, but we don’t recommended performing write operations while vacuuming\. It's more efficient to complete write operations before running the vacuum\. Also, any data that is written after a vacuum operation has been started can't be vacuumed by that operation\. In this case, a second vacuum operation is necessary\.
+ A vacuum operation might not be able to start if a load or insert operation is already in progress\. Vacuum operations temporarily require exclusive access to tables in order to start\. This exclusive access is required briefly, so vacuum operations don't block concurrent loads and inserts for any significant period of time\.
+ Vacuum operations are skipped when there is no work to do for a particular table; however, there is some overhead associated with discovering that the operation can be skipped\. If you know that a table is pristine or doesn't meet the vacuum threshold, don't run a vacuum operation against it\.
+ A DELETE ONLY vacuum operation on a small table might not reduce the number of blocks used to store the data, especially when the table has a large number of columns or the cluster uses a large number of slices per node\. These vacuum operations add one block per column per slice to account for concurrent inserts into the table, and there is potential for this overhead to outweigh the reduction in block count from the reclaimed disk space\. For example, if a 10\-column table on an 8\-node cluster occupies 1000 blocks before a vacuum, the vacuum doesn't reduce the actual block count unless more than 80 blocks of disk space are reclaimed because of deleted rows\. \(Each data block uses 1 MB\.\)

Automatic vacuum operations pause if any of the following conditions are met: 
+ A user runs a data definition language \(DDL\) operation, such as ALTER TABLE, that requires an exclusive lock on a table that automatic vacuum is currently working on\. 
+ A user triggers VACUUM on any table in the cluster \(only one VACUUM can run at a time\)\. 
+ A period of high cluster load\.

## Examples<a name="r_VACUUM_command-examples"></a>

Reclaim space and database and re\-sort rows in all tables based on the default 95 percent vacuum threshold\.

```
vacuum;
```

Reclaim space and re\-sort rows in the SALES table based on the default 95 percent threshold\. 

```
vacuum sales;
```

Always reclaim space and re\-sort rows in the SALES table\. 

```
vacuum sales to 100 percent;
```

Re\-sort rows in the SALES table only if fewer than 75 percent of rows are already sorted\. 

```
 vacuum sort only sales to 75 percent;
```

Reclaim space in the SALES table such that at least 75 percent of the remaining rows aren't marked for deletion following the vacuum\. 

```
vacuum delete only sales to 75 percent;
```

Reindex and then vacuum the LISTING table\. 

```
vacuum reindex listing;
```

The following command returns an error\. 

```
vacuum reindex listing to 75 percent;
```