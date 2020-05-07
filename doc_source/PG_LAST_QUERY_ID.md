# PG\_LAST\_QUERY\_ID<a name="PG_LAST_QUERY_ID"></a>

Returns the query ID of the most recently executed query in the current session\. If no queries have been executed in the current session, PG\_LAST\_QUERY\_ID returns \-1\. PG\_LAST\_QUERY\_ID does not return the query ID for queries that execute exclusively on the leader node\. For more information, see [Leader node–only functions](c_SQL_functions_leader_node_only.md)\.

## Syntax<a name="PG_LAST_QUERY_ID-synopsis"></a>

```
pg_last_query_id()
```

## Return type<a name="PG_LAST_QUERY_ID-return-type"></a>

Returns an integer\. 

## Example<a name="PG_LAST_QUERY_ID-example"></a>

The following query returns the ID of the latest query executed in the current session\. 

```
select pg_last_query_id();

pg_last_query_id
----------------
           5437
(1 row)
```

The following query returns the query ID and text of the most recently executed query in the current session\.

```
select query, trim(querytxt) as sqlquery
from stl_query
where query = pg_last_query_id();

query | sqlquery
------+--------------------------------------------------
 5437 | select name, loadtime from stl_file_scan where loadtime > 1000000;
(1 rows)
```