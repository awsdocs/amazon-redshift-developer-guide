# Serializable Isolation<a name="c_serial_isolation"></a>

Some applications require not only concurrent querying and loading, but also the ability to write to multiple tables or the same table concurrently\. In this context, *concurrently* means overlapping, not scheduled to run at precisely the same time\. Two transactions are considered to be concurrent if the second one starts before the first commits\. Concurrent operations can originate from different sessions that are controlled either by the same user or by different users\.

**Note**  
Amazon Redshift supports a default *automatic commit* behavior in which each separately\-executed SQL command commits individually\. If you enclose a set of commands in a transaction block \(defined by [BEGIN](r_BEGIN.md) and [END](r_END.md) statements\), the block commits as one transaction, so you can roll it back if necessary\. An exception to this behavior is the TRUNCATE command, which automatically commits all outstanding changes made in the current transaction without requiring an END statement\.

Concurrent write operations are supported in Amazon Redshift in a protective way, using write locks on tables and the principle of *serializable isolation*\. Serializable isolation preserves the illusion that a transaction running against a table is the only transaction that is running against that table\. For example, two concurrently running transactions, T1 and T2, must produce the same results as at least one of the following:

+ T1 and T2 run serially in that order

+ T2 and T1 run serially in that order

Concurrent transactions are invisible to each other; they cannot detect each other's changes\. Each concurrent transaction will create a snapshot of the database at the beginning of the transaction\. A database snapshot is created within a transaction on the first occurrence of most SELECT statements, DML commands such as COPY, DELETE, INSERT, UPDATE, and TRUNCATE, and the following DDL commands :

+ ALTER TABLE \(to add or drop columns\)

+ CREATE TABLE

+ DROP TABLE

+ TRUNCATE TABLE

If *any* serial execution of the concurrent transactions would produce the same results as their concurrent execution, those transactions are deemed "serializable" and can be run safely\. If no serial execution of those transactions would produce the same results, the transaction that executes a statement that would break serializability is aborted and rolled back\.

System catalog tables \(PG\) and other Amazon Redshift system tables \(STL and STV\) are not locked in a transaction; therefore, changes to database objects that arise from DDL and TRUNCATE operations are visible on commit to any concurrent transactions\.

For example, suppose that table A exists in the database when two concurrent transactions, T1 and T2, start\. If T2 returns a list of tables by selecting from the PG\_TABLES catalog table, and then T1 drops table A and commits, and then T2 lists the tables again, table A is no longer listed\. If T2 tries to query the dropped table, Amazon Redshift returns a "relation does not exist" error\. The catalog query that returns the list of tables to T2 or checks that table A exists is not subject to the same isolation rules as operations against user tables\.

Transactions for updates to these tables run in a *read committed* isolation mode\. PG\-prefix catalog tables do not support snapshot isolation\.

## Serializable Isolation for System Tables and Catalog Tables<a name="c_serial_isolation-serializable-isolation-for-tables"></a>

A database snapshot is also created in a transaction for any SELECT query that references a user\-created table or Amazon Redshift system table \(STL or STV\)\. SELECT queries that do not reference any table will not create a new transaction database snapshot, nor will any INSERT, DELETE, or UPDATE statements that operate solely on system catalog tables \(PG\)\.