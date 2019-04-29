# Overview of Stored Procedures in Amazon Redshift<a name="stored-procedure-create"></a>

Stored procedures are commonly used to encapsulate logic for data transformation, data validation, and business\-specific logic\. By combining multiple SQL steps into a stored procedure, you can reduce round trips between your applications and the database\.

For fine\-grained access control, you can create stored procedures to perform functions without giving a user access to the underlying tables\. For example, only the owner or a superuser can truncate a table, and a user needs write permission to insert data into a table\. Instead of granting a user permissions on the underlying tables, you can create a stored procedure that performs the task\. You then give the user permission to run the stored procedure\. 

A stored procedure with the DEFINER security attribute runs with the privileges of the stored procedure's owner\. By default, a stored procedure has INVOKER security, which means the procedure uses the permissions of the user that calls the procedure\. 

To create a stored procedure, use the [CREATE PROCEDURE](r_CREATE_PROCEDURE.md) command\. To run a procedure, use the [CALL](r_CALL_procedure.md) command\. Examples follow later in this section\.

**Note**  
Some clients might throw the following error when creating an Amazon Redshift stored procedure\.  

```
ERROR: 42601: unterminated dollar-quoted string at or near "$$
```
This error occurs due to the inability of the client to correctly parse the `CREATE PROCEDURE` statement with semicolons inside\. You can often work around this error by using the `Execute as single batch` or `Execute selected` option\. Or, you can use a client tool with better support for parsing `CREATE PROCEDURE` statements, for example TablePlus or SQLWorkbenchJ\. 

**Topics**
+ [Naming Stored Procedures](stored-procedure-naming.md)
+ [Security and Privileges for Stored Procedures](stored-procedure-security-and-privileges.md)
+ [Returning a Result Set](stored-procedure-result-set.md)
+ [Managing Transactions](stored-procedure-transaction-management.md)
+ [Trapping Errors](stored-procedure-trapping-errors.md)
+ [Logging Stored Procedures](c_PLpgSQL-logging.md)
+ [Limits and Differences for Stored Procedure Support](stored-procedure-constraints.md)

The following example shows a procedure with no output arguments\. By default, arguments are input \(IN\) arguments\.

```
CREATE OR REPLACE PROCEDURE test_sp1(f1 int, f2 varchar)
AS $$
BEGIN
  RAISE INFO 'f1 = %, f2 = %', f1, f2;
END;
$$ LANGUAGE plpgsql;

call test_sp1(5, 'abc');
INFO: f1 = 5, f2 = abc
CALL
```

The following example shows a procedure with output arguments\. Arguments are input \(IN\), input and output \(INOUT\), and output \(OUT\)\.

```
CREATE OR REPLACE PROCEDURE test_sp2(f1 IN int, f2 INOUT varchar(256), OUT varchar(256))
AS $$
DECLARE
  out_var alias for $3;
  loop_var int;
BEGIN
  IF f1 is null OR f2 is null THEN
    RAISE EXCEPTION 'input cannot be null';
  END IF;
  DROP TABLE if exists my_etl;
  CREATE TEMP TABLE my_etl(a int, b varchar);
    FOR loop_var IN 1..f1 LOOP
        insert into my_etl values (loop_var, f2);
        f2 := f2 || '+' || f2;
    END LOOP;
  SELECT INTO out_var count(*) from my_etl;
END;
$$ LANGUAGE plpgsql;


call test_sp2(2,'2019');

         f2          | column2
---------------------+---------
 2019+2019+2019+2019 | 2
(1 row)
```