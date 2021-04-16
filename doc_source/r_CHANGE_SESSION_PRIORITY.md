# CHANGE\_SESSION\_PRIORITY<a name="r_CHANGE_SESSION_PRIORITY"></a>

CHANGE\_SESSION\_PRIORITY enables superusers to immediately change the priority of any session in the system\. Only one session, user, or query can run with the priority `CRITICAL`\.

## Syntax<a name="r_CHANGE_SESSION_PRIORITY-synopsis"></a>

```
CHANGE_SESSION_PRIORITY(pid, priority)
```

## Arguments<a name="r_CHANGE_SESSION_PRIORITY-argument"></a>

 *pid*   
The process identifier of the session whose priority is changed\. The value `-1` refers to the current session\.

 *priority*   
The new priority to be assigned to the session\. This argument must be a string with the value `CRITICAL`, `HIGHEST`, `HIGH`, `NORMAL`, `LOW`, or `LOWEST`\. 

## Return type<a name="r_CHANGE_SESSION_PRIORITY-return-type"></a>

None

## Example<a name="r_CHANGE_SESSION_PRIORITY-example"></a>

The following example returns the process identifier of the server process handling the current session\.

```
select pg_backend_pid();
               
 pg_backend_pid
----------------
 30311
(1 row)
```

In this example, the priority is changed for the current session to `LOWEST`\.

```
select change_session_priority(30311, 'Lowest');
               
 change_session_priority
---------------------------------------------------------
Succeeded to change session priority. Changed session (pid:30311) priority to lowest.
(1 row)
```

In this example, the priority is changed for the current session to `HIGH`\.

```
select change_session_priority(-1, 'High');
 change_session_priority
---------------------------------------------------------------------
Succeeded to change session priority. Changed session (pid:30311) priority from lowest to high.
(1 row)
```

In the following example, a stored procedure is created to change a session priority\. Permission to run this stored procedure is granted to the database user `test_user`\.

```
CREATE OR REPLACE PROCEDURE sp_priority_low(pid IN int, result OUT varchar)
AS $$
BEGIN
  select change_session_priority(pid, 'low') into result;
END;
$$ LANGUAGE plpgsql
SECURITY DEFINER;
GRANT EXECUTE ON PROCEDURE sp_priority_low(int) TO test_user;
```

Then the database user named `test_user` calls the procedure\.

```
call sp_priority_low(pg_backend_pid()); 

                       result
-------------------------------------------------------
Success. Change session (pid:13155) priority to low.
```