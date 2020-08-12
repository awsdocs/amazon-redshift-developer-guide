# STL\_TR\_CONFLICT<a name="r_STL_TR_CONFLICT"></a>

Displays information to identify and resolve transaction conflicts with database tables\.

A transaction conflict occurs when two or more users are querying and modifying data rows from tables such that their transactions cannot be serialized\. The transaction that executes a statement that would break serializability is aborted and rolled back\. Every time a transaction conflict occurs, Amazon Redshift writes a data row to the STL\_TR\_CONFLICT system table containing details about the aborted transaction\. For more information, see [Serializable isolation](c_serial_isolation.md)\.

This view is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_TR_CONFLICT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_TR_CONFLICT.html)

## Sample query<a name="r_STL_TR_CONFLICT-sample-query"></a>

To return information about conflicts that involved a particular table, run a query that specifies the table ID: 

```
select * from stl_tr_conflict where table_id=100234
order by xact_start_ts;

xact_id|process_|      xact_start_ts       |        abort_time        |table_
       |id      |                          |                          |id
-------+--------+--------------------------+--------------------------+------
  1876 |  8551  |2010-03-30 09:19:15.852326|2010-03-30 09:20:17.582499|100234
  1928 | 15034  |2010-03-30 13:20:00.636045|2010-03-30 13:20:47.766817|100234
  1991 | 23753  |2010-04-01 13:05:01.220059|2010-04-01 13:06:06.94098 |100234
  2002 | 23679  |2010-04-01 13:17:05.173473|2010-04-01 13:18:27.898655|100234
(4 rows)
```

You can get the table ID from the DETAIL section of the error message for serializability violations \(error 1023\)\.