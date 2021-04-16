# Compute node–only functions<a name="c_SQL_functions_compute_node_only"></a>

Some Amazon Redshift queries must execute only on the compute nodes\. If a query references a user\-created table, the SQL runs on the compute nodes\.

A query that references only catalog tables \(tables with a PG prefix, such as PG\_TABLE\_DEF\) or that does not reference any tables, runs exclusively on the leader node\.

If a query that uses a compute\-node function doesn't reference a user\-defined table or Amazon Redshift system table returns the following error\.

```
[Amazon](500310) Invalid operation: One or more of the used functions must be applied on at least one user created table.
```

The documentation for each compute\-node only function includes a note stating that the function will return an error if the query doesn't references a user\-defined table or Amazon Redshift system table\.

The following SQL functions are compute\-node only functions:
+ LISTAGG
+ MEDIAN
+ PERCENTILE\_CONT
+ PERCENTILE\_DISC and APPROXIMATE PERCENTILE\_DISC