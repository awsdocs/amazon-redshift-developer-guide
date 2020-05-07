# Structure of PL/pgSQL<a name="c_PLpgSQL-structure"></a>

PL/pgSQL is a procedural language with many of the same constructs as other procedural languages\.  

**Topics**
+ [Block](#r_PLpgSQL-block)
+ [Variable declaration](#r_PLpgSQL-variable-declaration)
+ [Alias declaration](#r_PLpgSQL-alias-declaration)
+ [Built\-in variables](#r_PLpgSQL-builtin-variables)
+ [Record types](#r_PLpgSQL-record-type)

## Block<a name="r_PLpgSQL-block"></a>

PL/pgSQL is a block\-structured language\. The complete body of a procedure is defined in a block, which contains variable declarations and PL/pgSQL statements\. A statement can also be a nested block, or subblock\. 

End declarations and statements with a semicolon\. Follow the END keyword in a block or subblock with a semicolon\. Don't use semicolons after the keywords DECLARE and BEGIN\. 

You can write all keywords and identifiers in mixed uppercase and lowercase\. Identifiers are implicitly converted to lowercase unless enclosed in double quotation marks\.

A double hyphen \(\-\-\) starts a comment that extends to the end of the line\. A /\* starts a block comment that extends to the next occurrence of \*/\. You can't nest block comments\. However, you can enclose double\-hyphen comments in a block comment, and a double hyphen can hide the block comment delimiters /\* and \*/\.

Any statement in the statement section of a block can be a subblock\. You can use subblocks for logical grouping or to localize variables to a small group of statements\.

```
[ <<label>> ]
[ DECLARE
  declarations ]
BEGIN
  statements
END [ label ];
```

The variables declared in the declarations section preceding a block are initialized to their default values every time the block is entered\. In other words, they're not initialized only once per function call\.

The following shows an example\.

```
CREATE PROCEDURE update_value() AS $$
DECLARE
  value integer := 20;
BEGIN
  RAISE NOTICE 'Value here is %', value;  -- Value here is 20
  value := 50;
  --
  -- Create a subblock
  --
  DECLARE
    value integer := 80;
  BEGIN
    RAISE NOTICE 'Value here is %', value;  -- Value here is 80
  END;

  RAISE NOTICE 'Value here is %', value;  -- Value here is 50
END;
$$ LANGUAGE plpgsql;
```

Use a label to identify the block to use in an EXIT statement or to qualify the names of the variables declared in the block\.

Don't confuse the use of BEGIN/END for grouping statements in PL/pgSQL with the database commands for transaction control\. The BEGIN and END in PL/pgSQL are only for grouping\. They don't start or end a transaction\.

## Variable declaration<a name="r_PLpgSQL-variable-declaration"></a>

Declare all variables in a block, with the exception of loop variables, in the block's DECLARE section\. Variables can use any valid Amazon Redshift data type\. For supported data types, see [Data types](c_Supported_data_types.md)\. 

PL/pgSQL variables can be any Amazon Redshift supported data type, plus `RECORD` and `refcursor`\. For more information about `RECORD`, see [Record types](#r_PLpgSQL-record-type)\. For more information about `refcursor`, see [Cursors](c_PLpgSQL-statements.md#r_PLpgSQL-cursors)\. 

```
DECLARE
name [ CONSTANT ] type [ NOT NULL ] [ { DEFAULT | := } expression ];
```

Following, you can find example variable declarations\.

```
customerID integer;
numberofitems numeric(6);
link varchar;
onerow RECORD;
```

The loop variable of a FOR loop iterating over a range of integers is automatically declared as an integer variable\. 

The DEFAULT clause, if given, specifies the initial value assigned to the variable when the block is entered\. If the DEFAULT clause is not given, then the variable is initialized to the SQL NULL value\. The CONSTANT option prevents the variable from being assigned to, so that its value remains constant for the duration of the block\. If NOT NULL is specified, an assignment of a null value results in a runtime error\. All variables declared as NOT NULL must have a non\-null default value specified\.

The default value is evaluated every time the block is entered\. So, for example, assigning `now()` to a variable of type `timestamp` causes the variable to have the time of the current function call, not the time when the function was precompiled\.

```
quantity INTEGER DEFAULT 32;
url VARCHAR := 'http://mysite.com';
user_id CONSTANT INTEGER := 10;
```

The `refcursor` data type is the data type of cursor variables within stored procedures\. A `refcursor` value can be returned from within a stored procedure\. For more information, see [Returning a result set](stored-procedure-result-set.md)\.

## Alias declaration<a name="r_PLpgSQL-alias-declaration"></a>

If stored procedure's signature omits the argument name, you can declare an alias for the argument\.

```
name ALIAS FOR $n;
```

## Built\-in variables<a name="r_PLpgSQL-builtin-variables"></a>

The following built\-in variables are supported:
+ FOUND
+ SQLSTATE
+ SQLERRM
+ GET DIAGNOSTICS integer\_var := ROW\_COUNT;

FOUND is a special variable of type Boolean\. FOUND starts out false within each procedure call\. FOUND is set by the following types of statements:
+ SELECT INTO

  Sets FOUND to true if it returns a row, false if no row is returned\.
+ UPDATE, INSERT, and DELETE

  Sets FOUND to true if at least one row is affected, false if no row is affected\.
+ FETCH

  Sets FOUND to true if it returns a row, false if no row is returned\.
+ FOR statement

  Sets FOUND to true if the FOR statement iterates one or more times, and otherwise false\. This applies to all three variants of the FOR statement: integer FOR loops, record\-set FOR loops, and dynamic record\-set FOR loops\. 

  FOUND is set when the FOR loop exits\. Inside the execution of the loop, FOUND isn't modified by the FOR statement\. However, it can be changed by the execution of other statements within the loop body\.

The following shows an example\.

```
CREATE TABLE employee(empname varchar);
CREATE OR REPLACE PROCEDURE show_found()
AS  $$
DECLARE
  myrec record;
BEGIN
  SELECT INTO myrec * FROM employee WHERE empname = 'John';
  IF NOT FOUND THEN
    RAISE EXCEPTION 'employee John not found';
  END IF;
END;
$$ LANGUAGE plpgsql;
```

Within an exception handler, the special variable SQLSTATE contains the error code that corresponds to the exception that was raised\. The special variable SQLERRM contains the error message associated with the exception\. These variables are undefined outside exception handlers and throw an error if used\.

The following shows an example\.

```
CREATE OR REPLACE PROCEDURE sqlstate_sqlerrm() AS
$$
BEGIN
  UPDATE employee SET firstname = 'Adam' WHERE lastname = 'Smith';
  EXECUTE 'select invalid';
  EXCEPTION WHEN OTHERS THEN
  RAISE INFO 'error message SQLERRM %', SQLERRM;
  RAISE INFO 'error message SQLSTATE %', SQLSTATE;
END;
$$ LANGUAGE plpgsql;
```

ROW\_COUNT is used with the GET DIAGNOSTICS command\. It shows the number of rows processed by the last SQL command sent down to the SQL engine\.

The following shows an example\.

```
CREATE OR REPLACE PROCEDURE sp_row_count() AS
$$
DECLARE
  integer_var int;
BEGIN
  INSERT INTO tbl_row_count VALUES(1);
  GET DIAGNOSTICS integer_var := ROW_COUNT;
  RAISE INFO 'rows inserted = %', integer_var;
END;
$$ LANGUAGE plpgsql;
```

## Record types<a name="r_PLpgSQL-record-type"></a>

A RECORD type is not a true data type, only a placeholder\. Record type variables assume the actual row structure of the row that they are assigned during a SELECT or FOR command\. The substructure of a record variable can change each time it is assigned a value\. Until a record variable is first assigned to, it has no substructure\. Any attempt to access a field in it throws a runtime error\.

```
name RECORD;
```

The following shows an example\.

```
CREATE TABLE tbl_record(a int, b int);
INSERT INTO tbl_record VALUES(1, 2);
CREATE OR REPLACE PROCEDURE record_example()
LANGUAGE plpgsql
AS $$
DECLARE
  rec RECORD;
BEGIN
  FOR rec IN SELECT a FROM tbl_record
  LOOP
    RAISE INFO 'a = %', rec.a;
  END LOOP;
END;
$$;
```