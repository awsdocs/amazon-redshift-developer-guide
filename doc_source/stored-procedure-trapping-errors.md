# Trapping errors<a name="stored-procedure-trapping-errors"></a>

When a query or command in a stored procedure causes an error, subsequent queries don't run and the transaction is rolled back\. But you can handle errors using an EXCEPTION block\.

**Note**  
The default behavior is that an error will cause subsequent queries not to run, even when there are no additional error\-generating conditions in the stored procedure\.

```
[ <<label>> ]
[ DECLARE
  declarations ]
BEGIN
  statements
EXCEPTION
  WHEN OTHERS THEN
    statements
END;
```

When an exception occurs, and you add an exception\-handling block, you can write RAISE statements and most other PL/pgSQL statements\. For example, you can raise an exception with a custom message or insert a record into a logging table\.

When entering the exception\-handling block, the current transaction is rolled back and a new transaction is created to run the statements in the block\. If the statements in the block run without error, the transaction is committed and the exception is re\-thrown\. Lastly, the stored procedure exits\.

The only supported condition in an exception block is OTHERS, which matches every error type except query cancellation\. Also, if an error occurs in an exception\-handling block, it can be caught by an outer exception\-handling block\.

When an error occurs inside the NONATOMIC procedure, the error is not re\-thrown if it is handled by an exception block\. See the PL/pgSQL statement `RAISE` to throw an exception caught by the exception handling block\. This statement is only valid in exception handling blocks\. For more information see [RAISE](c_PLpgSQL-statements.md#r_PLpgSQL-messages-errors)\.

There are several system tables available to help you gather information about various types of errors\. For more information, see [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md), [STL\_ERROR](r_STL_ERROR.md), and [SYS\_STREAM\_SCAN\_ERRORS](r_SYS_STREAM_SCAN_ERRORS.md)\. There are also additional system tables you can use to troubleshoot errors\. More information about these can be found at [System tables reference](cm_chap_system-tables.md)\.

## Example<a name="stored-procedure-trapping-errors-examples"></a>

The following example shows how to write statements in the exception\-handling block\. The stored procedure is using default transaction management behavior\.

```
CREATE TABLE employee (firstname varchar, lastname varchar);
INSERT INTO employee VALUES ('Tomas','Smith');
CREATE TABLE employee_error_log (message varchar);

CREATE OR REPLACE PROCEDURE update_employee_sp() AS
$$
BEGIN
    UPDATE employee SET firstname = 'Adam' WHERE lastname = 'Smith';
    EXECUTE 'select invalid';
EXCEPTION WHEN OTHERS THEN
    RAISE INFO 'An exception occurred.';
    INSERT INTO employee_error_log VALUES ('Error message: ' || SQLERRM);
END;
$$ 
LANGUAGE plpgsql;

CALL update_employee_sp();

INFO:  An exception occurred.
ERROR:  column "invalid" does not exist
CONTEXT:  SQL statement "select invalid"
PL/pgSQL function "update_employee_sp" line 3 at execute statement
```

In this example, if we call `update_employee_sp`, the informational message *An exception occurred\.* is raised and the error message is inserted in the logging table's `employee_error_log` log\. The original exception is thrown again before the stored procedure exits\. The following queries show records that result from running the example\.

```
SELECT * from employee;

firstname | lastname 
-----------+----------
 Tomas     | Smith

SELECT * from employee_error_log;

          message                     
------------------------------------------------
 Error message: column "invalid" does not exist
```

For more information about RAISE, including formatting help and a list of additional levels, see [Supported PL/pgSQL statements](c_PLpgSQL-statements.md)\.

The following example shows how to write statements in the exception\-handling block\. The stored procedure is using NONATOMIC transaction management behavior\. In this example, there is no error thrown back to caller after the procedure call completes\. The UPDATE statement is not rolled back due to the error in the next statement\. The informational message is raised and the error message is inserted in the logging table\.

```
CREATE TABLE employee (firstname varchar, lastname varchar); 
INSERT INTO employee VALUES ('Tomas','Smith'); 
CREATE TABLE employee_error_log (message varchar);

-- Create the SP in NONATOMIC mode
CREATE OR REPLACE PROCEDURE update_employee_sp_2() NONATOMIC AS
$$
BEGIN
    UPDATE employee SET firstname = 'Adam' WHERE lastname = 'Smith';
    EXECUTE 'select invalid';
EXCEPTION WHEN OTHERS THEN
    RAISE INFO 'An exception occurred.';
    INSERT INTO employee_error_log VALUES ('Error message: ' || SQLERRM);
END;
$$ 
LANGUAGE plpgsql;

CALL update_employee_sp_2();
INFO:  An exception occurred.
CALL

SELECT * from employee;

 firstname | lastname 
-----------+----------
 Adam      | Smith
(1 row)

SELECT * from employee_error_log;

                    message                     
------------------------------------------------
 Error message: column "invalid" does not exist
(1 row)
```

This example shows how to create a procedure with two sub blocks\. When the stored procedure is called, the error from the first sub block is handled by its exception handling block\. After the first sub block completes, the procedure continues to execute the second sub block\. You can see from the result that no error is thrown when the procedure call completes\. The UPDATE and INSERT operations on table employee are committed\. Error messages from both exception blocks are inserted in the logging table\.

```
CREATE TABLE employee (firstname varchar, lastname varchar); 
INSERT INTO employee VALUES ('Tomas','Smith'); 
CREATE TABLE employee_error_log (message varchar);

CREATE OR REPLACE PROCEDURE update_employee_sp_3() NONATOMIC AS
$$
BEGIN
    BEGIN
        UPDATE employee SET firstname = 'Adam' WHERE lastname = 'Smith';
        EXECUTE 'select invalid1';
    EXCEPTION WHEN OTHERS THEN
        RAISE INFO 'An exception occurred in the first block.';
        INSERT INTO employee_error_log VALUES ('Error message: ' || SQLERRM);
    END;
    BEGIN
        INSERT INTO employee VALUES ('Edie','Robertson');
        EXECUTE 'select invalid2';
    EXCEPTION WHEN OTHERS THEN
        RAISE INFO 'An exception occurred in the second block.';
        INSERT INTO employee_error_log VALUES ('Error message: ' || SQLERRM);
    END;
END;
$$ 
LANGUAGE plpgsql;

CALL update_employee_sp_3();
INFO:  An exception occurred in the first block.
INFO:  An exception occurred in the second block.
CALL

SELECT * from employee;

 firstname | lastname  
-----------+-----------
 Adam      | Smith
 Edie      | Robertson
(2 rows)

SELECT * from employee_error_log;

                     message                     
-------------------------------------------------
 Error message: column "invalid1" does not exist
 Error message: column "invalid2" does not exist
(2 rows)
```