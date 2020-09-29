# Serializable isolation<a name="c_serial_isolation"></a>

Some applications require not only concurrent querying and loading, but also the ability to write to multiple tables or the same table concurrently\. In this context, *concurrently* means overlapping, not scheduled to run at precisely the same time\. Two transactions are considered to be concurrent if the second one starts before the first commits\. Concurrent operations can originate from different sessions that are controlled either by the same user or by different users\.

**Note**  
Amazon Redshift supports a default *automatic commit* behavior in which each separately executed SQL command commits individually\. If you enclose a set of commands in a transaction block \(defined by [BEGIN](r_BEGIN.md) and [END](r_END.md) statements\), the block commits as one transaction, so you can roll it back if necessary\. Exceptions to this behavior are the TRUNCATE and VACUUM commands, which automatically commit all outstanding changes made in the current transaction\.   
Some SQL clients issue BEGIN and COMMIT commands automatically, so the client controls whether a group of statements are run as a transaction or each individual statement is run as its own transaction\. Check the documentation for the interface you are using\. For example, when using the Amazon Redshift JDBC driver, a JDBC `PreparedStatement` with a query string that contains multiple \(semicolon separated\) SQL commands runs all the statements as a single transaction\. In contrast, if you use SQL Workbench/J and set AUTO COMMIT ON, then if you run multiple statements, each statement runs as its own transaction\. 

Concurrent write operations are supported in Amazon Redshift in a protective way, using write locks on tables and the principle of *serializable isolation*\. Serializable isolation preserves the illusion that a transaction running against a table is the only transaction that is running against that table\. For example, two concurrently running transactions, T1 and T2, must produce the same results as at least one of the following:
+ T1 and T2 run serially in that order\.
+ T2 and T1 run serially in that order\.

Concurrent transactions are invisible to each other; they cannot detect each other's changes\. Each concurrent transaction will create a snapshot of the database at the beginning of the transaction\. A database snapshot is created within a transaction on the first occurrence of most SELECT statements, DML commands such as COPY, DELETE, INSERT, UPDATE, and TRUNCATE, and the following DDL commands :
+ ALTER TABLE \(to add or drop columns\)
+ CREATE TABLE
+ DROP TABLE
+ TRUNCATE TABLE

If *any* serial execution of the concurrent transactions would produce the same results as their concurrent execution, those transactions are deemed "serializable" and can be run safely\. If no serial execution of those transactions would produce the same results, the transaction that executes a statement that would break serializability is aborted and rolled back\.

System catalog tables \(PG\) and other Amazon Redshift system tables \(STL and STV\) are not locked in a transaction; therefore, changes to database objects that arise from DDL and TRUNCATE operations are visible on commit to any concurrent transactions\.

For example, suppose that table A exists in the database when two concurrent transactions, T1 and T2, start\. If T2 returns a list of tables by selecting from the PG\_TABLES catalog table, and then T1 drops table A and commits, and then T2 lists the tables again, table A is no longer listed\. If T2 tries to query the dropped table, Amazon Redshift returns a "relation does not exist" error\. The catalog query that returns the list of tables to T2 or checks that table A exists is not subject to the same isolation rules as operations against user tables\.

Transactions for updates to these tables run in a *read committed* isolation mode\. PG\-prefix catalog tables do not support snapshot isolation\.

## Serializable isolation for system tables and catalog tables<a name="c_serial_isolation-serializable-isolation-for-tables"></a>

A database snapshot is also created in a transaction for any SELECT query that references a user\-created table or Amazon Redshift system table \(STL or STV\)\. SELECT queries that do not reference any table will not create a new transaction database snapshot, nor will any INSERT, DELETE, or UPDATE statements that operate solely on system catalog tables \(PG\)\.

## How to fix serializable isolation errors<a name="c_serial_isolation-serializable-isolation-troubleshooting"></a>

### ERROR:1023 DETAIL: Serializable isolation violation on table in Redshift<a name="c_serial_isolation-serialization-isolation-1023"></a>

When Amazon Redshift detects a serializable isolation error, you see an error message such as the following\.

```
ERROR:1023 DETAIL: Serializable isolation violation on table in Redshift
```

To address a serializable isolation error, you can try the following methods:
+ Move any operations that don't have to be in the same atomic transaction outside of the transaction\.

  This method applies when individual operations inside two transactions cross\-reference each other in a way that can affect the outcome of the other transaction\. For example, the following two sessions each start a transaction\.  

  ```
  Session1_Redshift=# begin;
  ```

  ```
  Session2_Redshift=# begin;
  ```

  The result of a SELECT statement in each transaction might be affected by an INSERT statement in the other\. In other words, suppose that you run the following statements serially, in any order\. In every case, the result is one of the SELECT statements returning one more row than if the transactions were run concurrently\. There is no order in which the operations can run serially that produces the same result as when run concurrently\. Thus, the last operation that is run results in a serializable isolation error\.

  ```
  Session1_Redshift=# select * from tab1;
  Session1_Redshift=# insert into tab2 values (1);
  ```

  ```
  Session2_Redshift=# insert into tab1 values (1);
  Session2_Redshift=# select * from tab2;
  ```

  In many cases, the result of the SELECT statements isn't important\. In other words, the atomicity of the operations in the transactions isn't important\. In these cases, move the SELECT statements outside of their transactions, as shown in the following examples\.

  ```
  Session1_Redshift=# begin;
  Session1_Redshift=# insert into tab1 values (1)
  Session1_Redshift=# end;
  Session1_Redshift=# select * from tab2;
  ```

  ```
  Session2_Redshift # select * from tab1;
  Session2_Redshift=# begin;
  Session2_Redshift=# insert into tab2 values (1)
  Session2_Redshift=# end;
  ```

  In these examples, there are no cross\-references in the transactions\. The two INSERT statements don't affect each other\. In these examples, there is at least one order in which the transactions can run serially and produce the same result as if run concurrently\. This means that the transactions are serializable\.
+ Force serialization by locking all tables in each session\.

  The [LOCK](r_LOCK.md) command blocks operations that can result in serializable isolation errors\. When you use the LOCK command, be sure to do the following:
  + Lock all tables affected by the transaction, including those affected by read\-only SELECT statements inside the transaction\.
  + Lock tables in the same order, regardless of the order that operations are performed in\.
  + Lock all tables at the beginning of the transaction, before performing any operations\.
+ Retry the aborted transaction\.

  A transaction can encounter a serializable isolation error if it conflicts with operations performed by another concurrent transaction\. If the conflicting transactions don't need to run at the same time, simply retrying the aborted transaction might succeed\. If the issue persists, try one of the other methods\.

### ERROR:1018 DETAIL: Relation does not exist<a name="c_serial_isolation-serialization-isolation-1018"></a>

When you run concurrent Amazon Redshift operations in different sessions, you see an error message such as the following\.

```
ERROR: 1018 DETAIL: Relation does not exist.
```

Transactions in Amazon Redshift follow snapshot isolation\. After a transaction begins, Amazon Redshift takes a snapshot of the database\. For the entire lifecycle of the transaction, the transaction operates on the state of the database as reflected in the snapshot\. If the transaction reads from a table that doesn't exist in the snapshot, it throws the 1018 error message shown previously\. Even when another concurrent transaction creates a table after the transaction has taken the snapshot, the transaction can't read from the newly created table\.

To address this serialization isolation error, you can try to move the start of the transaction to a point where you know the table exists\.

If the table is created by another transaction, this point is at least after that transaction has been committed\. Also, ensure that no concurrent transaction has been committed that might have dropped the table\.

```
session1 = # BEGIN;
session1 = # DROP TABLE A;
session1 = # COMMIT;
```

```
session2 = # BEGIN;
```

```
session3 = # BEGIN;
session3 = # CREATE TABLE A (id INT);
session3 = # COMMIT;
```

```
session2 = # SELECT * FROM A;
```

The last operation that is run as the read operation by session2 results in a serializable isolation error\. This error happens when session2 takes a snapshot and the table has already been dropped by a committed session1\. In other words, even though a concurrent session3 has created the table, session2 doesn't see the table becuase it's not in the snapshot\.

To resolve this error, you can reorder the sessions as follows\.

```
session1 = # BEGIN;
session1 = # DROP TABLE A;
session1 = # COMMIT;
```

```
session3 = # BEGIN;
session3 = # CREATE TABLE A (id INT);
session3 = # COMMIT;
```

```
session2 = # BEGIN;
session2 = # SELECT * FROM A;
```

Now when session2 takes its snapshot, session3 has already been committed, and the table is in the database\. Session2 can read from the table without any error\.