# Determine the process ID of a running query<a name="determine_pid"></a>

In the previous example you learned how to obtain the query ID and process ID \(PID\) for a completed query from the SVL\_QLOG view\.

You might need to find the PID for a query that is still running\. For example, you will need the PID if you need to cancel a query that is taking too long to run\. You can query the STV\_RECENTS system table to obtain a list of process IDs for running queries, along with the corresponding query string\. If your query returns multiple PIDs, you can look at the query text to determine which PID you need\.

To determine the PID of a running query, issue the following SELECT statement: 

```
select pid, user_name, starttime, query
from stv_recents
where status='Running';
```