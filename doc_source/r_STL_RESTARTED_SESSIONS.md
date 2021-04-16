# STL\_RESTARTED\_SESSIONS<a name="r_STL_RESTARTED_SESSIONS"></a>

To maintain continuous availability following certain internal events, Amazon Redshift might restart an active session with a new process ID \(PID\)\. When Amazon Redshift restarts a session, STL\_RESTARTED\_SESSIONS records the new PID and the old PID\.

 For more information, see the examples following in this section\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_RESTARTED_SESSIONS-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_RESTARTED_SESSIONS.html)

## Sample queries<a name="r_STL_RESTARTED_SESSIONS-sample-queries"></a>

The following example joins STL\_RESTARTED\_SESSIONS with STL\_SESSIONS to show user names for sessions that have been restarted\. 

```
select process, stl_restarted_sessions.newpid, user_name
from stl_sessions
inner join stl_restarted_sessions on stl_sessions.process = stl_restarted_sessions.oldpid
order by process;

...
```