# Assigning queries to queues<a name="cm-c-executing-queries"></a>

The following examples assign queries to queues according to user groups and query groups\.

## Assigning queries to queues based on user groups<a name="cm-c-executing-queries-assigning-queries-to-queues-based-on-user-groups"></a>

If a user group name is listed in a queue definition, queries run by members of that user group are assigned to the corresponding queue\. The following example creates user groups and adds users to groups by using the SQL commands [CREATE USER](r_CREATE_USER.md), [CREATE GROUP](r_CREATE_GROUP.md), and [ALTER GROUP](r_ALTER_GROUP.md)\.

```
create group admin_group with user admin246, admin135, sec555;
create user vp1234 in group ad_hoc_group password 'vpPass1234';
alter group admin_group add user analyst44, analyst45, analyst46;
```

## Assigning a query to a query group<a name="cm-c-executing-queries-assigning-a-query-to-a-query-group"></a>

You can assign a query to a queue at runtime by assigning your query to the appropriate query group\. Use the SET command to begin a query group\.

```
SET query_group TO group_label             
```

Here, **group\_label* *is a query group label that is listed in the WLM configuration\.

All queries that you run after the `SET query_group` command run as members of the specified query group until you either reset the query group or end your current login session\. For information about setting and resetting Amazon Redshift objects, see [SET](r_SET.md) and [RESET](r_RESET.md) in the SQL Command Reference\.

The query group labels that you specify must be included in the current WLM configuration; otherwise, the *SET query\_group* command has no effect on query queues\.

The label defined in the TO clause is captured in the query logs so that you can use the label for troubleshooting\. For information about the query\_group configuration parameter, see [query\_group](r_query_group.md) in the Configuration Reference\.

The following example runs two queries as part of the query group 'priority' and then resets the query group\.

```
set query_group to 'priority';
select count(*)from stv_blocklist;
select query, elapsed, substring from svl_qlog order by query desc limit 5; 
reset query_group;
```

## Assigning queries to the superuser queue<a name="cm-c-executing-queries-assigning-superuser-queue"></a>

To assign a query to the superuser queue, log on to Amazon Redshift as a superuser and then run the query in the superuser group\. When you are done, reset the query group so that subsequent queries do not run in the superuser queue\.

The following example assigns two commands to run in the superuser queue\.

```
set query_group to 'superuser';

analyze;
vacuum; 
reset query_group;
```

To view a list of superusers, query the PG\_USER system catalog table\.

```
select * from pg_user where usesuper = 'true';
```