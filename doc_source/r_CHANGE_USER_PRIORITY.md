# CHANGE\_USER\_PRIORITY<a name="r_CHANGE_USER_PRIORITY"></a>

CHANGE\_USER\_PRIORITY enables superusers to modify the priority of all queries issued by a user that are either running or waiting in workload management \(WLM\)\. Only one user, session, or query can run with the priority `CRITICAL`\.

## Syntax<a name="r_CHANGE_USER_PRIORITY-synopsis"></a>

```
CHANGE_USER_PRIORITY(user_name, priority)
```

## Arguments<a name="r_CHANGE_USER_PRIORITY-argument"></a>

 *user\_name*   
The database user name whose query priority is changed\. 

 *priority*   
The new priority to be assigned to all queries issued by `user_name`\. This argument must be a string with the value `CRITICAL`, `HIGHEST`, `HIGH`, `NORMAL`, `LOW`, `LOWEST`, or `RESET`\. Only superusers can change the priority to `CRITICAL`\. Changing the priority to `RESET` removes the priority setting for `user_name`\.

## Return type<a name="r_CHANGE_USER_PRIORITY-return-type"></a>

None

## Example<a name="r_CHANGE_USER_PRIORITY-example"></a>

In the following example, the priority is changed for the user `analysis_user` to `LOWEST`\.

```
select change_user_priority('analysis_user', 'lowest');
                                change_user_priority
-------------------------------------------------------------------------------------
 Succeeded to change user priority. Changed user (analysis_user) priority to lowest.
(1 row)
```

In the next statement, the priority is changed to `LOW`\.

```
select change_user_priority('analysis_user', 'low');
                                     change_user_priority
----------------------------------------------------------------------------------------------
 Succeeded to change user priority. Changed user (analysis_user) priority from Lowest to low.
(1 row)
```

In this example, the priority is reset\.

```
select change_user_priority('analysis_user', 'reset');
                 change_user_priority
-------------------------------------------------------
 Succeeded to reset priority for user (analysis_user).
(1 row)
```