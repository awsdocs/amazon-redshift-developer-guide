# Leader node–only functions<a name="c_SQL_functions_leader_node_only"></a>

Some Amazon Redshift queries are distributed and executed on the compute nodes; other queries execute exclusively on the leader node\.

The leader node distributes SQL to the compute nodes when a query references user\-created tables or system tables \(tables with an STL or STV prefix and system views with an SVL or SVV prefix\)\. A query that references only catalog tables \(tables with a PG prefix, such as PG\_TABLE\_DEF\) or that does not reference any tables, runs exclusively on the leader node\.

Some Amazon Redshift SQL functions are supported only on the leader node and are not supported on the compute nodes\. A query that uses a leader\-node function must execute exclusively on the leader node, not on the compute nodes, or it will return an error\.

The documentation for each leader\-node only function includes a note stating that the function will return an error if it references user\-defined tables or Amazon Redshift system tables\.

For more information, see [SQL functions supported on the leader node](c_sql-functions-leader-node.md)\.

The following SQL functions are leader\-node only functions and are not supported on the compute nodes:

System information functions
+ CURRENT\_SCHEMA
+ CURRENT\_SCHEMAS
+ HAS\_DATABASE\_PRIVILEGE
+ HAS\_SCHEMA\_PRIVILEGE
+ HAS\_TABLE\_PRIVILEGE

The following leader\-node only functions are deprecated:

Date functions
+ AGE
+ CURRENT\_TIME
+ CURRENT\_TIMESTAMP
+ LOCALTIME
+ ISFINITE
+ NOW

String functions
+ GET\_BIT
+ GET\_BYTE
+ SET\_BIT
+ SET\_BYTE
+ TO\_ASCII