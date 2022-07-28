# Trapping errors<a name="stored-procedure-trapping-errors"></a>

When an error occurs in a stored procedure, no additional code is run and the transaction is rolled back\. But you can handle errors using an EXCEPTION block\.

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

## Example<a name="stored-procedure-trapping-errors-examples"></a>

The following example shows how to write statements in the exception\-handling block\.

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