# Limits and differences for stored procedure support<a name="stored-procedure-constraints"></a>

The following constraints apply when you use Amazon Redshift stored procedures\.

## Differences between Amazon Redshift and PostgreSQL for stored procedure support<a name="stored-procedure-differences"></a>

 The following are differences between stored procedure support in Amazon Redshift and PostgreSQL:
+ Amazon Redshift doesn't support subtransactions, and hence has limited support for exception handling blocks\.

## Limits<a name="stored-procedure-limits"></a>

The following are limits on stored procedures in Amazon Redshift:
+ The maximum number of stored procedures for a database is 3000\.
+ The maximum size of the source code for a procedure is 2 MB\.
+ The maximum number of explicit and implicit cursors that you can open concurrently in a user session is one\. FOR loops that iterate over the result set of a SQL statement open implicit cursors\. Nested cursors aren't supported\.
+ Explicit and implicit cursors have the same restrictions on the result set size as standard Amazon Redshift cursors\. For more information, see [Cursor constraints](declare.md#declare-constraints)\. 
+ The maximum number of levels for nested calls is 16\.
+ The maximum number of procedure parameters is 32 for input arguments and 32 for output arguments\.
+ The maximum number of variables in a stored procedure is 1,024\.
+ Any SQL command that requires its own transaction context isn't supported inside a stored procedure\. Examples are VACUUM, ALTER TABLE APPEND, and CREATE EXTERNAL TABLE\.
+ The `registerOutParameter` method call through the Java Database Connectivity \(JDBC\) driver isn't supported for the `refcursor` data type\.   For an example of using the `refcursor` data type, see [Returning a result set](stored-procedure-result-set.md)\.