# Best practices for RLS performance<a name="t_rls_performance"></a>

Following are best practices to ensure better performance from Amazon Redshift on tables protected by RLS\.

## Safety of operators and functions<a name="t_rls_safe_operators"></a>

When querying RLS\-protected tables, the usage of certain operators or functions may lead to performance degradation\. Amazon Redshift classifies operators and functions either as safe or unsafe for querying RLS\-protected tables\. A function or operator is classified as RLS\-safe when it doesn't have any observable side\-effects depending on the inputs\. In particular, a RLS\-safe function or operator can't be one of the following:
+ Outputs an input value, or any value that is dependent on the input value, with or without an error message\.
+ Fails or returns errors that are dependent on the input value\.

RLS\-unsafe operators include:
+ Arithmetic operators — \+, \-, /, \*, %\.
+ Text operators — LIKE and SIMILAR TO\.
+ Cast operators\.
+ UDFs\.

Use the following SELECT statement to check the safety of operators and functions\.

```
SELECT proname, proc_is_rls_safe(oid) FROM pg_proc;
```

Amazon Redshift imposes restrictions on the order of evaluation of user predicates containing RLS\-unsafe operators and functions when planning queries on RLS\-protected tables\. Queries referencing RLS\-unsafe operators or functions might cause performance degradation when querying RLS\-protected tables\. Performance can degrade significantly when Amazon Redshift can't push RLS\-unsafe predicates down to base table scans to take advantage of sort keys\. For better performance, avoid queries using RLS\-unsafe predicates that take advantage of a sort key\. To verify that Amazon Redshift is able to push down operators and functions, you can use EXPLAIN statements in combination with the system permission EXPLAIN RLS\.

## Result caching<a name="t_rls_result_cache"></a>

To reduce query runtime and improve system performance, Amazon Redshift caches the results of certain types of queries in the memory on the leader node\.

Amazon Redshift uses cached results for a new query scanning RLS\-protected tables when all the conditions for unprotected tables are true and when all of the following are true:
+ The tables or views in the policy haven't been modified\.
+ The policy doesn't use a function that must be evaluated each time it's run, such as GETDATE or CURRENT\_USER\.

For better performance, avoid using policy predicates that don't satisfy the preceding conditions\.

For more information on result caching in Amazon Redshift, see [ Result caching ](c_challenges_achieving_high_performance_queries.md#result-caching)\.

## Complex policies<a name="t_rls_complex_policies"></a>

For better performance, avoid using complex policies with subqueries that join multiple tables\.