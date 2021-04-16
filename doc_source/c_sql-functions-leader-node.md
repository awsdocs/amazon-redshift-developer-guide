# SQL functions supported on the leader node<a name="c_sql-functions-leader-node"></a>

Some Amazon Redshift queries are distributed and executed on the compute nodes, and other queries run exclusively on the leader node\.

The leader node distributes SQL to the compute nodes whenever a query references user\-created tables or system tables \(tables with an STL or STV prefix and system views with an SVL or SVV prefix\)\. A query that references only catalog tables \(tables with a PG prefix, such as PG\_TABLE\_DEF, which reside on the leader node\) or that does not reference any tables, runs exclusively on the leader node\.

Some Amazon Redshift SQL functions are supported only on the leader node and are not supported on the compute nodes\. A query that uses a leader\-node function must run exclusively on the leader node, not on the compute nodes, or it will return an error\.

The documentation for each function that must run exclusively on the leader node includes a note stating that the function will return an error if it references user\-defined tables or Amazon Redshift system tables\. See [Leader node–only functions](c_SQL_functions_leader_node_only.md) for a list of functions that run exclusively on the leader node\.

## Examples<a name="c_sql-functions-leader-node-examples"></a>

The CURRENT\_SCHEMA function is a leader\-node only function\. In this example, the query does not reference a table, so it runs exclusively on the leader node\.

```
select current_schema();
```

The result is as follows\.

```
current_schema
---------------
public
(1 row)
```

In the next example, the query references a system catalog table, so it runs exclusively on the leader node\.

```
select * from pg_table_def
where schemaname = current_schema() limit 1;

 schemaname | tablename | column |   type   | encoding | distkey | sortkey | notnull
------------+-----------+--------+----------+----------+---------+---------+---------
 public     | category  | catid  | smallint | none     | t       |       1 | t
(1 row)
```

In the next example, the query references an Amazon Redshift system table that resides on the compute nodes, so it returns an error\.

```
select current_schema(), userid from users;

INFO:  Function "current_schema()" not supported.
ERROR:  Specified types or functions (one per INFO message) not supported on Amazon Redshift tables.
```