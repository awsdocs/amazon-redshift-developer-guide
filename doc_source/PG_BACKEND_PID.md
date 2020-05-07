# PG\_BACKEND\_PID<a name="PG_BACKEND_PID"></a>

Returns the process ID \(PID\) of the server process handling the current session\.

**Note**  
The PID is not globally unique\. It can be reused over time\.

## Syntax<a name="PG_BACKEND_PID-synopsis"></a>

```
pg_backend_pid()
```

## Return type<a name="PG_BACKEND_PID-return-type"></a>

Returns an integer\.

## Example<a name="PG_BACKEND_PID-example"></a>

You can correlate PG\_BACKEND\_PID\(\) with log tables to retrieve information for the current session\. For example, the following query returns the query ID and a portion of the query text for queries executed in the current session\.

```
select query, substring(text,1,40)
from stl_querytext
where pid =  PG_BACKEND_PID()
order by query desc;

 query |                substring
-------+------------------------------------------
 14831 | select query, substring(text,1,40) from
 14827 | select query, substring(path,0,80) as pa
 14826 | copy category from 's3://dw-tickit/manif
 14825 | Count rows in target table
 14824 | unload ('select * from category') to 's3
(5 rows)
```

You can correlate PG\_BACKEND\_PID\(\) with the pid column in the following log tables \(exceptions are noted in parentheses\):
+ [STL\_CONNECTION\_LOG](r_STL_CONNECTION_LOG.md)
+ [STL\_DDLTEXT](r_STL_DDLTEXT.md)
+ [STL\_ERROR](r_STL_ERROR.md)
+ [STL\_QUERY](r_STL_QUERY.md)
+ [STL\_QUERYTEXT](r_STL_QUERYTEXT.md)
+ [STL\_SESSIONS](r_STL_SESSIONS.md) \(process\)
+ [STL\_TR\_CONFLICT](r_STL_TR_CONFLICT.md)
+ [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md)
+ [STV\_ACTIVE\_CURSORS](r_STV_ACTIVE_CURSORS.md)
+ [STV\_INFLIGHT](r_STV_INFLIGHT.md)
+ [STV\_LOCKS](r_STV_LOCKS.md) \(lock\_owner\_pid\)
+ [STV\_RECENTS](r_STV_RECENTS.md) \(process\_id\)