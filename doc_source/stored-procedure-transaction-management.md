# Managing Transactions<a name="stored-procedure-transaction-management"></a>

The default automatic commit behavior causes each separately\-executed SQL command to commit individually\. A call to a stored procedure is treated as a single SQL command\. The SQL statements inside a procedure behave as if they are in a transaction block that implicitly begins when the call starts and ends when the call finishes\. A nested call to another procedure is treated like any other SQL statement and operates within the context of the same transaction as the caller\. For more information about automatic commit behavior, see [Serializable Isolation](c_serial_isolation.md)\.

However, when you call a stored procedure from within a user specified transaction block \(defined by BEGIN\.\.\.COMMIT\), all statements in the stored procedure run in the context of the user specified transaction\. The procedure doesn't commit implicitly on exit\. The caller controls the procedure commit or rollback\.

If any error is encountered while running a stored procedure, all changes made in the current transaction are rolled back\.

An exception to this behavior is the TRUNCATE command\. In Amazon Redshift, TRUNCATE issues a commit implicitly\. This behavior stays the same in the context of stored procedures\. When a TRUNCATE statement is issued from within a stored procedure, it commits the current transaction and begins a new one\. All statements that follow the TRUNCATE statement run in the context of this new transaction till another TRUNCATE statement is encountered or the stored procedure exits\.

When you use TRUNCATE from within a stored procedure, the following constraints apply:
+ If the stored procedure is called from within a transaction block, it can't issue a TRUNCATE statement from within its own body or any nested procedure call\.
+ If the stored procedure is created with `SET config` options, it can't issue a TRUNCATE statement from within its own body or from any nested procedure call\. 
+ Any cursor that is open \(explicitly or implicitly\) is closed automatically when a TRUNCATE statement is processed\. For constraints on explicit and implicit cursors, see [Limits and Differences for Stored Procedure Support](stored-procedure-constraints.md)\.

For more information, see [TRUNCATE](r_TRUNCATE.md)\.

When working with stored procedures consider the following:
+ The BEGIN and END statements in PL/pgSQL are only for grouping\. They don't start or end a transaction\. For more information, see [Block](c_PLpgSQL-structure.md#r_PLpgSQL-block)\. 
+ Transaction control statements, such as COMMIT and ROLLBACK, aren't supported from within a stored procedure\. For more information, see [Limits and Differences for Stored Procedure Support](stored-procedure-constraints.md)\.

The following example demonstrates transaction behavior when calling a stored procedure from within an explicit transaction block\. The two insert statements issued from outside the stored procedure and the one from within it are all part of the same transaction \(3382\)\. The transaction is committed when the user issues the explicit commit\.

```
CREATE OR REPLACE PROCEDURE sp_insert_table_a(a int) LANGUAGE plpgsql
AS $$
BEGIN
  INSERT INTO test_table_a values (a);
END;
$$;

Begin;
  insert into test_table_a values (1);
  Call sp_insert_table_a(2);
  insert into test_table_a values (3);
Commit; 

select userid, xid, pid, type, trim(text) as stmt_text 
from svl_statementtext where pid = pg_backend_pid() order by xid , starttime , sequence;

 userid | xid  | pid |  type   |               stmt_text
--------+------+-----+---------+----------------------------------------
    103 | 3382 | 599 | UTILITY | Begin;
    103 | 3382 | 599 | QUERY   | insert into test_table_a values (1);
    103 | 3382 | 599 | UTILITY | Call sp_insert_table_a(2);
    103 | 3382 | 599 | QUERY   | INSERT INTO test_table_a values ( $1 )
    103 | 3382 | 599 | QUERY   | insert into test_table_a values (3);
    103 | 3382 | 599 | UTILITY | COMMIT
```

When the same statements are issued from outside of an explicit transaction block, and the session has autocommit set to ON, each statement executes in its own transaction\.

```
insert into test_table_a values (1);
Call sp_insert_table_a(2);
insert into test_table_a values (3);

select userid, xid, pid, type, trim(text) as stmt_text 
from svl_statementtext where pid = pg_backend_pid() order by xid , starttime , sequence;

 userid | xid  | pid |  type   |                                                                    stmt_text
--------+------+-----+---------+-------------------------------------------------------------------------------------------------------------------------------------------------
    103 | 3388 | 599 | QUERY   | insert into test_table_a values (1);
    103 | 3388 | 599 | UTILITY | COMMIT
    103 | 3389 | 599 | UTILITY | Call sp_insert_table_a(2);
    103 | 3389 | 599 | QUERY   | INSERT INTO test_table_a values ( $1 )
    103 | 3389 | 599 | UTILITY | COMMIT
    103 | 3390 | 599 | QUERY   | insert into test_table_a values (3);
    103 | 3390 | 599 | UTILITY | COMMIT
```

The following example issues a TRUNCATE statement after inserting into `test_table_a`\. The TRUNCATE statement issues an implicit commit that commits the current transaction \(3335\) and starts a new one \(3336\)\. The new transaction is committed when the procedure exits\.

```
CREATE OR REPLACE PROCEDURE sp_truncate_proc(a int, b int) LANGUAGE plpgsql
AS $$
BEGIN
  INSERT INTO test_table_a values (a);
  TRUNCATE test_table_b;
  INSERT INTO test_table_b values (b);
END;
$$;

Call sp_truncate_proc(1,2);

select userid, xid, pid, type, trim(text) as stmt_text 
from svl_statementtext where pid = pg_backend_pid() order by xid , starttime , sequence;

 userid | xid  |  pid  |  type   |                                                                                             stmt_text
--------+------+-------+---------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    103 | 3335 | 23636 | UTILITY | Call sp_truncate_proc(1,2);
    103 | 3335 | 23636 | QUERY   | INSERT INTO test_table_a values ( $1 )
    103 | 3335 | 23636 | UTILITY | TRUNCATE test_table_b
    103 | 3335 | 23636 | UTILITY | COMMIT
    103 | 3336 | 23636 | QUERY   | INSERT INTO test_table_b values ( $1 )
    103 | 3336 | 23636 | UTILITY | COMMIT
```

The following example issues a TRUNCATE from a nested call\. The TRUNCATE commits all work done so far in the outer and inner procedures in a transaction \(3344\)\. It starts a new transaction \(3345\)\. The new transaction is committed when the outer procedure exits\.

```
CREATE OR REPLACE PROCEDURE sp_inner(c int, d int) LANGUAGE plpgsql
AS $$
BEGIN
  INSERT INTO inner_table values (c);
  TRUNCATE outer_table;
  INSERT INTO inner_table values (d);
END;
$$;

CREATE OR REPLACE PROCEDURE sp_outer(a int, b int, c int, d int) LANGUAGE plpgsql
AS $$
BEGIN
  INSERT INTO outer_table values (a);
  Call sp_inner(c, d);
  INSERT INTO outer_table values (b);
END;
$$;

Call sp_outer(1, 2, 3, 4);

select userid, xid, pid, type, trim(text) as stmt_text 
from svl_statementtext where pid = pg_backend_pid() order by xid , starttime , sequence;

 userid | xid  |  pid  |  type   |                                                                                              stmt_text
--------+------+-------+---------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    103 | 3344 | 23636 | UTILITY | Call sp_outer(1, 2, 3, 4);
    103 | 3344 | 23636 | QUERY   | INSERT INTO outer_table values ( $1 )
    103 | 3344 | 23636 | UTILITY | CALL sp_inner( $1 , $2 )
    103 | 3344 | 23636 | QUERY   | INSERT INTO inner_table values ( $1 )
    103 | 3344 | 23636 | UTILITY | TRUNCATE outer_table
    103 | 3344 | 23636 | UTILITY | COMMIT
    103 | 3345 | 23636 | QUERY   | INSERT INTO inner_table values ( $1 )
    103 | 3345 | 23636 | QUERY   | INSERT INTO outer_table values ( $1 )
    103 | 3345 | 23636 | UTILITY | COMMIT
```

The following example shows that cursor `cur1` was closed when the TRUNCATE statement committed\.

```
CREATE OR REPLACE PROCEDURE sp_open_cursor_truncate()
LANGUAGE plpgsql
AS $$
DECLARE
  rec RECORD;
  cur1 cursor for select * from test_table_a order by 1;
BEGIN
  open cur1;
  TRUNCATE table test_table_b;
  Loop
    fetch cur1 into rec;
    raise info '%', rec.c1;
    exit when not found;
  End Loop;
END
$$;

call sp_open_cursor_truncate();
ERROR: cursor "cur1" does not exist
CONTEXT: PL/pgSQL function "sp_open_cursor_truncate" line 8 at fetch
```

The following example issues a TRUNCATE statement and can't be called from within an explicit transaction block\.

```
CREATE OR REPLACE PROCEDURE sp_truncate_atomic() LANGUAGE plpgsql
AS $$
BEGIN
  TRUNCATE test_table_b;
END;
$$;

Begin;
  Call sp_truncate_atomic();
ERROR: TRUNCATE cannot be invoked from a procedure that is executing in an atomic context.
HINT: Try calling the procedure as a top-level call i.e. not from within an explicit transaction block. 
Or, if this procedure (or one of its ancestors in the call chain) was created with SET config options, recreate the procedure without them.
CONTEXT: SQL statement "TRUNCATE test_table_b"
PL/pgSQL function "sp_truncate_atomic" line 2 at SQL statement
```