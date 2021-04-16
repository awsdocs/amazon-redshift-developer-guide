# FETCH<a name="fetch"></a>

Retrieves rows using a cursor\. For information about declaring a cursor, see [DECLARE](declare.md)\.

FETCH retrieves rows based on the current position within the cursor\. When a cursor is created, it is positioned before the first row\. After a FETCH, the cursor is positioned on the last row retrieved\. If FETCH runs off the end of the available rows, such as following a FETCH ALL, the cursor is left positioned after the last row\. 

FORWARD 0 fetches the current row without moving the cursor; that is, it fetches the most recently fetched row\. If the cursor is positioned before the first row or after the last row, no row is returned\. 

When the first row of a cursor is fetched, the entire result set is materialized on the leader node, in memory or on disk, if needed\. Because of the potential negative performance impact of using cursors with large result sets, we recommend using alternative approaches whenever possible\. For more information, see [Performance considerations when using cursors](declare.md#declare-performance)\.

For more information, see [DECLARE](declare.md), [CLOSE](close.md)\. 

## Syntax<a name="fetch-synopsis"></a>

```
FETCH [ NEXT | ALL | {FORWARD [ count | ALL ] } ] FROM cursor
```

## Parameters<a name="fetch-parameters"></a>

NEXT  
Fetches the next row\. This is the default\.

ALL  
Fetches all remaining rows\. \(Same as FORWARD ALL\.\) ALL isn't supported for single\-node clusters\.

FORWARD \[ *count* \| ALL \]   
Fetches the next *count* rows, or all remaining rows\. `FORWARD 0` fetches the current row\. For single\-node clusters, the maximum value for count is `1000`\. FORWARD ALL isn't supported for single\-node clusters\. 

*cursor*   
Name of the new cursor\. 

## FETCH example<a name="fetch-example"></a>

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