# PG\_CANCEL\_BACKEND<a name="PG_CANCEL_BACKEND"></a>

Cancels a query\. PG\_CANCEL\_BACKEND is functionally equivalent to the [CANCEL](r_CANCEL.md) command\. You can cancel queries currently being run by your user\. Superusers can cancel any query\.

## Syntax<a name="PG_CANCEL_BACKEND-synopsis"></a>

```
pg_cancel_backend( pid )
```

## Arguments<a name="PG_CANCEL_BACKEND-arguments"></a>

 *pid*   
The process ID \(PID\) of the query to be canceled\. You cannot cancel a query by specifying a query ID; you must specify the query's process ID\. Requires an integer value\.

## Return type<a name="PG_CANCEL_BACKEND-return-type"></a>

None

## Usage Notes<a name="PG_CANCEL_BACKEND-usage-notes"></a>

If queries in multiple sessions hold locks on the same table, you can use the [PG\_TERMINATE\_BACKEND](PG_TERMINATE_BACKEND.md) function to terminate one of the sessions, which forces any currently running transactions in the terminated session to release all locks and roll back the transaction\. Query the PG\_\_LOCKS catalog table to view currently held locks\. If you cannot cancel a query because it is in transaction block \(BEGIN … END\), you can terminate the session in which the query is running by using the PG\_TERMINATE\_BACKEND function\. 

## Examples<a name="PG_CANCEL_BACKEND-example"></a>

To cancel a currently running query, first retrieve the process ID for the query that you want to cancel\. To determine the process IDs for all currently running queries, execute the following command: 

```
select pid, trim(starttime) as start, 
duration, trim(user_name) as user,
substring (query,1,40) as querytxt
from stv_recents
where status = 'Running';

 pid |       starttime        | duration |   user   |    querytxt
-----+------------------------+----------+----------+--------------------------
 802 | 2013-10-14 09:19:03.55 |      132 | dwuser | select venuename from venue 
 834 | 2013-10-14 08:33:49.47 |  1250414 | dwuser | select * from listing;
 964 | 2013-10-14 08:30:43.29 |   326179 | dwuser | select sellerid from sales
```

The following statement cancels the query with process ID 802:

```
select pg_cancel_backend(802);
```