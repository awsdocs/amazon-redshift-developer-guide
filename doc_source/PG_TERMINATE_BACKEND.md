# PG\_TERMINATE\_BACKEND<a name="PG_TERMINATE_BACKEND"></a>

Terminates a session\. You can terminate a session owned by your user\. A superuser can terminate any session\.

## Syntax<a name="PG_TERMINATE_BACKEND-synopsis"></a>

```
pg_terminate_backend( pid )
```

## Arguments<a name="PG_TERMINATE_BACKEND-arguments"></a>

*pid*  
The process ID of the session to be terminated\. Requires an integer value\.

## Return type<a name="PG_TERMINATE_BACKEND-return-type"></a>

None

## Usage notes<a name="PG_TERMINATE_BACKEND-usage-notes"></a>

 If you are close to reaching the limit for concurrent connections, use PG\_TERMINATE\_BACKEND to terminate idle sessions and free up the connections\. For more information, see [Limits in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/amazon-redshift-limits.html)\. 

If queries in multiple sessions hold locks on the same table, you can use PG\_TERMINATE\_BACKEND to terminate one of the sessions, which forces any currently running transactions in the terminated session to release all locks and roll back the transaction\. Query the PG\_\_LOCKS catalog table to view currently held locks\. 

If a query is not in a transaction block \(BEGIN … END\), you can cancel the query by using the [CANCEL](r_CANCEL.md) command or the [PG\_CANCEL\_BACKEND](PG_CANCEL_BACKEND.md) function\. 

## Examples<a name="PG_TERMINATE_BACKEND-example"></a>

The following statement queries the SVV\_TRANSACTIONS table to view all locks in effect for current transactions:

```
select * from svv_transactions;
txn_owner | txn_db    | xid   | pid  | txn_start           | lock_mode       | lockable_object_type | relation | granted
----------+-----------+-------+------+---------------------+-----------------+----------------------+----------+--------
  rsuser  | dev       | 96178 | 8585 | 2017-04-12 20:13:07 | AccessShareLock | relation             |    51940 | true   
  rsuser  | dev       | 96178 | 8585 | 2017-04-12 20:13:07 | AccessShareLock | relation             |    52000 | true   
  rsuser  | dev       | 96178 | 8585 | 2017-04-12 20:13:07 | AccessShareLock | relation             |   108623 | true   
  rsuser  | dev       | 96178 | 8585 | 2017-04-12 20:13:07 | ExclusiveLock   | transactionid        |          | true
```

The following statement terminates the session holding the locks:

```
select pg_terminate_backend(8585); 
```