# Step 6: Cancel a query<a name="cancel_query"></a>

If a user issues a query that is taking too long or is consuming excessive cluster resources, you might need to cancel the query\. For example, a user might want to create a list of ticket sellers that includes the seller's name and quantity of tickets sold\. The following query selects data from the SALES table USERS table and joins the two tables by matching SELLERID and USERID in the WHERE clause\.

```
select sellerid, firstname, lastname, sum(qtysold)
from sales, users
where sales.sellerid = users.userid
group by sellerid, firstname, lastname
order by 4 desc;
```

**Note**  
This is a complex query\. For this tutorial, you don't need to worry about how this query is constructed\.

The previous query runs in seconds and returns 2,102 rows\.

Suppose the user forgets to put in the WHERE clause\.

```
select sellerid, firstname, lastname, sum(qtysold)
from sales, users
group by sellerid, firstname, lastname
order by 4 desc;
```

The result set will include all of the rows in the SALES table multiplied by all the rows in the USERS table \(49989\*3766\)\. This is called a Cartesian join, and it is not recommended\. The result is over 188 million rows and takes a long time to run\.

To cancel a running query, use the CANCEL command with the query's PID\.

To find the process ID, query the STV\_RECENTS table, as shown in the previous step\. The following example shows how you can make the results more readable by using the TRIM function to trim trailing spaces and by showing only the first 20 characters of the query string\.

```
select pid, trim(user_name), starttime, substring(query,1,20) 
from stv_recents
where status='Running';
```

The result looks something like this:

```
  pid  |   btrim    |         starttime          |      substring
-------+------------+----------------------------+----------------------
 18764 | masteruser | 2013-03-28 18:39:49.355918 | select sellerid, fir
(1 row)
```

To cancel the query with PID 18764, issue the following command:

```
cancel 18764;
```

**Note**  
The CANCEL command will not abort a transaction\. To abort or roll back a transaction, you must use the ABORT or ROLLBACK command\. To cancel a query associated with a transaction, first cancel the query then abort the transaction\.

If the query that you canceled is associated with a transaction, use the ABORT or ROLLBACK\. command to cancel the transaction and discard any changes made to the data:

```
abort;
```

Unless you are signed on as a superuser, you can cancel only your own queries\. A superuser can cancel all queries\.

## Cancel a query from another session<a name="cancel_query-cancel-a-query-from-another-session"></a>

If your query tool does not support running queries concurrently, you will need to start another session to cancel the query\. For example, SQLWorkbench, which is the query tool we use in the Amazon Redshift Getting Started, does not support multiple concurrent queries\. To start another session using SQLWorkbench, select File, New Window and connect using the same connection parameters\. Then you can find the PID and cancel the query\.

## Cancel a query using the superuser queue<a name="cancel_query-cancel-a-query-using-the-superuser-queue"></a>

If your current session has too many queries running concurrently, you might not be able to run the CANCEL command until another query finishes\. In that case, you will need to issue the CANCEL command using a different workload management query queue\.

Workload management enables you to execute queries in different query queues so that you don't need to wait for another query to complete\. The workload manager creates a separate queue, called the Superuser queue, that you can use for troubleshooting\. To use the Superuser queue, you must be logged on a superuser and set the query group to 'superuser' using the SET command\. After running your commands, reset the query group using the RESET command\.

To cancel a query using the Superuser queue, issue these commands:

```
set query_group to 'superuser';
cancel 18764;
reset query_group;
```

For information about managing query queues, see [Implementing workload management](cm-c-implementing-workload-management.md)\.