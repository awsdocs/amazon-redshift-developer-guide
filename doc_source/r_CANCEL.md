# CANCEL<a name="r_CANCEL"></a>

Cancels a database query that is currently running\.

The CANCEL command requires the process ID of the running query and displays a confirmation message to verify that the query was cancelled\.

## Syntax<a name="r_CANCEL-synopsis"></a>

```
CANCEL process_id [ 'message' ]
```

## Parameters<a name="r_CANCEL-parameters"></a>

 *process\_id*   
Process ID corresponding to the query that you want to cancel\.

'*message*'   
An optional confirmation message that displays when the query cancellation completes\. If you don't specify a message, Amazon Redshift displays the default message as verification\. You must enclose the message in single quotation marks\.

## Usage notes<a name="r_CANCEL-usage-notes"></a>

You can't cancel a query by specifying a *query ID*; you must specify the query's *process ID* \(PID\)\. You can only cancel queries currently being run by your user\. Superusers can cancel all queries\.

If queries in multiple sessions hold locks on the same table, you can use the [PG\_TERMINATE\_BACKEND](PG_TERMINATE_BACKEND.md) function to terminate one of the sessions, which forces any currently running transactions in the terminated session to release all locks and roll back the transaction\. Query the [STV\_LOCKS](r_STV_LOCKS.md) system table to view currently held locks\. 

Following certain internal events, Amazon Redshift might restart an active session and assign a new PID\. If the PID has changed, you might receive the following error message:

```
Session <PID> does not exist. The session PID might have changed. Check the stl_restarted_sessions system table for details.
```

To find the new PID, query the [STL\_RESTARTED\_SESSIONS](r_STL_RESTARTED_SESSIONS.md) system table and filter on the `oldpid` column\.

```
select oldpid, newpid from stl_restarted_sessions where oldpid = 1234;
```

## Examples<a name="r_CANCEL-examples"></a>

To cancel a currently running query, first retrieve the process ID for the query that you want to cancel\. To determine the process IDs for all currently running queries, type the following command: 

```
select pid, starttime, duration,
trim(user_name) as user,
trim (query) as querytxt
from stv_recents
where status = 'Running';

pid |         starttime          | duration |   user   |    querytxt
-----+----------------------------+----------+----------+-----------------
802 | 2008-10-14 09:19:03.550885 |      132 | dwuser | select
venuename from venue where venuestate='FL', where venuecity not in
('Miami' , 'Orlando');
834 | 2008-10-14 08:33:49.473585 |  1250414 | dwuser | select *
from listing;
964 | 2008-10-14 08:30:43.290527 |   326179 | dwuser | select
sellerid from sales where qtysold in (8, 10);
```

Check the query text to determine which process id \(PID\) corresponds to the query that you want to cancel\.

Type the following command to use PID 802 to cancel that query: 

```
cancel 802;
```

The session where the query was running displays the following message: 

```
ERROR:  Query (168) cancelled on user's request
```

where `168` is the query ID \(not the process ID used to cancel the query\)\.

Alternatively, you can specify a custom confirmation message to display instead of the default message\. To specify a custom message, include your message in single quotation marks at the end of the CANCEL command: 

```
cancel 802 'Long-running query';
```

The session where the query was running displays the following message: 

```
ERROR:  Long-running query
```