# STV\_LOCKS<a name="r_STV_LOCKS"></a>

Use the STV\_LOCKS table to view any current updates on tables in the database\.

Amazon Redshift locks tables to prevent two users from updating the same table at the same time\. While the STV\_LOCKS table shows all current table updates, query the [STL\_TR\_CONFLICT](r_STL_TR_CONFLICT.md) table to see a log of lock conflicts\. Use the [SVV\_TRANSACTIONS](r_SVV_TRANSACTIONS.md) view to identify open transactions and lock contention issues\.

STV\_LOCKS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_LOCKS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_LOCKS.html)

## Sample query<a name="r_STV_LOCKS-sample-query"></a>

To view all locks taking place in current transactions, type the following command: 

```
select table_id, last_update, lock_owner, lock_owner_pid from stv_locks;
```

This query returns the following sample output, which displays three locks currently in effect: 

```
 table_id |        last_update         | lock_owner | lock_owner_pid
----------+----------------------------+------------+----------------
100004  | 2008-12-23 10:08:48.882319 |       1043 |           5656
100003  | 2008-12-23 10:08:48.779543 |       1043 |           5656
100140  | 2008-12-23 10:08:48.021576 |       1043 |           5656
(3 rows)
```