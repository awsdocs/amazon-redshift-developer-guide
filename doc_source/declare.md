# DECLARE<a name="declare"></a>

Defines a new cursor\. Use a cursor to retrieve a few rows at a time from the result set of a larger query\. 

When the first row of a cursor is fetched, the entire result set is materialized on the leader node, in memory or on disk, if needed\. Because of the potential negative performance impact of using cursors with large result sets, we recommend using alternative approaches whenever possible\. For more information, see [Performance considerations when using cursors](#declare-performance)\.

You must declare a cursor within a transaction block\. Only one cursor at a time can be open per session\. 

For more information, see [FETCH](fetch.md), [CLOSE](close.md)\.

## Syntax<a name="declare-synopsis"></a>

```
DECLARE cursor_name CURSOR FOR query
```

## Parameters<a name="declare-parameters"></a>

*cursor\_name*   
Name of the new cursor\. 

 *query*   
A SELECT statement that populates the cursor\.

## DECLARE CURSOR usage notes<a name="declare-usage"></a>

If your client application uses an ODBC connection and your query creates a result set that is too large to fit in memory, you can stream the result set to your client application by using a cursor\. When you use a cursor, the entire result set is materialized on the leader node, and then your client can fetch the results incrementally\. 

**Note**  
To enable cursors in ODBC for Microsoft Windows, enable the **Use Declare/Fetch** option in the ODBC DSN you use for Amazon Redshift\. We recommend setting the ODBC cache size, using the **Cache Size** field in the ODBC DSN options dialog, to 4,000 or greater on multi\-node clusters to minimize round trips\. On a single\-node cluster, set Cache Size to 1,000\.

Because of the potential negative performance impact of using cursors, we recommend using alternative approaches whenever possible\. For more information, see [Performance considerations when using cursors](#declare-performance)\.

Amazon Redshift cursors are supported with the following limitations:
+ Only one cursor at a time can be open per session\. 
+ Cursors must be used within a transaction \(BEGIN … END\)\. 
+ The maximum cumulative result set size for all cursors is constrained based on the cluster node type\. If you need larger result sets, you can resize to an XL or 8XL node configuration\.

  For more information, see [Cursor constraints](#declare-constraints)\. 

## Cursor constraints<a name="declare-constraints"></a>

When the first row of a cursor is fetched, the entire result set is materialized on the leader node\. If the result set doesn't fit in memory, it is written to disk as needed\. To protect the integrity of the leader node, Amazon Redshift enforces constraints on the size of all cursor result sets, based on the cluster's node type\.

The following table shows the maximum total result set size for each cluster node type\. Maximum result set sizes are in megabytes\.


| Node type | Maximum result set per cluster \(MB\) | 
| --- | --- | 
|   DS1 or DS2 XL single node   |   64000   | 
|   DS1 or DS2 XL multiple nodes   |   1800000   | 
|   DS1 or DS2 8XL multiple nodes   |   14400000   | 
|   RA3 16XL multiple nodes   |   14400000   | 
|   DC1 Large single node   |   16000   | 
|   DC1 Large multiple nodes   |   384000   | 
|   DC1 8XL multiple nodes   |   3000000   | 
|   DC2 Large single node   |   8000   | 
|   DC2 Large multiple nodes   |   192000   | 
|   DC2 8XL multiple nodes   |   3200000   | 
|   RA3 4XL multiple nodes   |   3200000   | 

To view the active cursor configuration for a cluster, query the [STV\_CURSOR\_CONFIGURATION](r_STV_CURSOR_CONFIGURATION.md) system table as a superuser\. To view the state of active cursors, query the [STV\_ACTIVE\_CURSORS](r_STV_ACTIVE_CURSORS.md) system table\. Only the rows for a user's own cursors are visible to the user, but a superuser can view all cursors\.

## Performance considerations when using cursors<a name="declare-performance"></a>

Because cursors materialize the entire result set on the leader node before beginning to return results to the client, using cursors with very large result sets can have a negative impact on performance\. We strongly recommend against using cursors with very large result sets\. In some cases, such as when your application uses an ODBC connection, cursors might be the only feasible solution\. If possible, we recommend using these alternatives:
+ Use [UNLOAD](r_UNLOAD.md) to export a large table\. When you use UNLOAD, the compute nodes work in parallel to transfer the data directly to data files on Amazon Simple Storage Service\. For more information, see [Unloading data](c_unloading_data.md)\. 
+ Set the JDBC fetch size parameter in your client application\. If you use a JDBC connection and you are encountering client\-side out\-of\-memory errors, you can enable your client to retrieve result sets in smaller batches by setting the JDBC fetch size parameter\. For more information, see [Setting the JDBC fetch size parameter](queries-troubleshooting.md#set-the-JDBC-fetch-size-parameter)\. 

## DECLARE CURSOR example<a name="declare-example"></a>

The following example declares a cursor named LOLLAPALOOZA to select sales information for the Lollapalooza event, and then fetches rows from the result set using the cursor:

```
-- Begin a transaction

begin;

-- Declare a cursor

declare lollapalooza cursor for 
select eventname, starttime, pricepaid/qtysold as costperticket, qtysold
from sales, event
where sales.eventid = event.eventid
and eventname='Lollapalooza';

-- Fetch the first 5 rows in the cursor lollapalooza:

fetch forward 5 from lollapalooza;

  eventname   |      starttime      | costperticket | qtysold
--------------+---------------------+---------------+---------
 Lollapalooza | 2008-05-01 19:00:00 |   92.00000000 |       3
 Lollapalooza | 2008-11-15 15:00:00 |  222.00000000 |       2
 Lollapalooza | 2008-04-17 15:00:00 |  239.00000000 |       3
 Lollapalooza | 2008-04-17 15:00:00 |  239.00000000 |       4
 Lollapalooza | 2008-04-17 15:00:00 |  239.00000000 |       1
(5 rows)

-- Fetch the next row:

fetch next from lollapalooza;

  eventname   |      starttime      | costperticket | qtysold
--------------+---------------------+---------------+---------
 Lollapalooza | 2008-10-06 14:00:00 |  114.00000000 |       2

-- Close the cursor and end the transaction:

close lollapalooza;
commit;
```