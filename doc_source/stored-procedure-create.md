# Overview of stored procedures in Amazon Redshift<a name="stored-procedure-create"></a>

Stored procedures are commonly used to encapsulate logic for data transformation, data validation, and business\-specific logic\. By combining multiple SQL steps into a stored procedure, you can reduce round trips between your applications and the database\.

For fine\-grained access control, you can create stored procedures to perform functions without giving a user access to the underlying tables\. For example, only the owner or a superuser can truncate a table, and a user needs write permission to insert data into a table\. Instead of granting a user permissions on the underlying tables, you can create a stored procedure that performs the task\. You then give the user permission to run the stored procedure\. 

A stored procedure with the DEFINER security attribute runs with the privileges of the stored procedure's owner\. By default, a stored procedure has INVOKER security, which means the procedure uses the permissions of the user that calls the procedure\. 

To create a stored procedure, use the [CREATE PROCEDURE](r_CREATE_PROCEDURE.md) command\. To run a procedure, use the [CALL](r_CALL_procedure.md) command\. Examples follow later in this section\.

**Note**  
Some clients might throw the following error when creating an Amazon Redshift stored procedure\.  

```
ERROR: 42601: [Amazon](500310) unterminated dollar-quoted string at or near "$$
```
This error occurs due to the inability of the client to correctly parse the CREATE PROCEDURE statement with semicolons delimiting statements and with dollar sign \($\) quoting\. This results in only a part of the statement sent to the Amazon Redshift server\. You can often work around this error by using the `Run as batch` or `Execute selected` option of the client\.   
For example, when using an Aginity client, use the `Run entire script as batch` option\. When using SQL Workbench/J, we recommend version 124\. When using SQL Workbench/J version 125, consider specifying an alternate delimiter as a workaround\. Because the CREATE PROCEDURE contains SQL statements delimited with a semicolon \(;\), defining an alternate delimiter such as a forward slash \(/\) and placing it at the end of the CREATE PROCEDURE statement sends the entire statement to the Amazon Redshift server for processing\. As shown in the following example\.  

```
CREATE OR REPLACE PROCEDURE test()
AS $$
BEGIN
  SELECT 1 a;
END;
$$
LANGUAGE plpgsql
;
/
```
For more information, see [Alternate delimiter](http://www.sql-workbench.net/manual/profiles.html#profile-alternate-delimiter) in the SQL Workbench/J documentation\. Or use a client with better support for parsing CREATE PROCEDURE statements, such as the [Query editor in the Amazon Redshift console](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor.html) or TablePlus\. 

**Topics**
+ [Naming stored procedures](stored-procedure-naming.md)
+ [Security and privileges for stored procedures](stored-procedure-security-and-privileges.md)
+ [Returning a result set](stored-procedure-result-set.md)
+ [Managing transactions](stored-procedure-transaction-management.md)
+ [Trapping errors](stored-procedure-trapping-errors.md)
+ [Logging stored procedures](c_PLpgSQL-logging.md)
+ [Limits and differences for stored procedure support](stored-procedure-constraints.md)

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
CREATE OR REPLACE PROCEDURE test_sp2(f1 IN int, f2 INOUT varchar(256), out_var OUT varchar(256))
AS $$
DECLARE
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