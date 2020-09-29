# Supported PL/pgSQL statements<a name="c_PLpgSQL-statements"></a>

 PL/pgSQL statements augment SQL commands with procedural constructs, including looping and conditional expressions, to control logical flow\. Most SQL commands can be used, including data modification language \(DML\) such as COPY, UNLOAD and INSERT, and data definition language \(DDL\) such as CREATE TABLE\. For a list of comprehensive SQL commands, see [SQL commands](c_SQL_commands.md)\. In addition, the following PL/pgSQL statements are supported by Amazon Redshift\. 

**Topics**
+ [Assignment](#r_PLpgSQL-assignment)
+ [SELECT INTO](#r_PLpgSQL-select-into)
+ [No\-op](#r_PLpgSQL-no-op)
+ [Dynamic SQL](#r_PLpgSQL-dynamic-sql)
+ [Return](#r_PLpgSQL-return)
+ [Conditionals: IF](#r_PLpgSQL-conditionals-if)
+ [Conditionals: CASE](#r_PLpgSQL-conditionals-case)
+ [Loops](#r_PLpgSQL-loops)
+ [Cursors](#r_PLpgSQL-cursors)
+ [RAISE](#r_PLpgSQL-messages-errors)
+ [Transaction control](#r_PLpgSQL-transaction-control)

## Assignment<a name="r_PLpgSQL-assignment"></a>

The assignment statement assigns a value to a variable\. The expression must return a single value\.

```
identifier := expression;
```

Using the nonstandard `=` for assignment, instead of `:=`, is also accepted\.

If the data type of the expression doesn't match the variable's data type or the variable has a size or precision, the result value is implicitly converted\.

The following shows examples\.

```
customer_number := 20;
tip := subtotal * 0.15;
```

## SELECT INTO<a name="r_PLpgSQL-select-into"></a>

The SELECT INTO statement assigns the result of multiple columns \(but only one row\) into a record variable or list of scalar variables\.

```
SELECT INTO target select_expressions FROM ...;
```

In the preceding syntax, *target* can be a record variable or a comma\-separated list of simple variables and record fields\. The *select\_expressions* list and the remainder of the command are the same as in regular SQL\.

If a variable list is used as *target*, the selected values must exactly match the structure of the target, or a runtime error occurs\. When a record variable is the target, it automatically configures itself to the row type of the query result columns\.

The INTO clause can appear almost anywhere in the SELECT statement\. It usually appears just after the SELECT clause, or just before FROM clause\. That is, it appears just before or just after the *select\_expressions* list\.

If the query returns zero rows, NULL values are assigned to *target*\. If the query returns multiple rows, the first row is assigned to *target* and the rest are discarded\. Unless the statement contains an ORDER BY, the first row is not deterministic\.

To determine whether the assignment returned at least one row, use the special FOUND variable\.

```
SELECT INTO customer_rec * FROM cust WHERE custname = lname;
IF NOT FOUND THEN
  RAISE EXCEPTION 'employee % not found', lname;
END IF;
```

To test whether a record result is null, you can use the IS NULL conditional\. There is no way to determine whether any additional rows might have been discarded\. The following example handles the case where no rows have been returned\.

```
CREATE OR REPLACE PROCEDURE select_into_null(return_webpage OUT varchar(256))
AS $$
DECLARE
  customer_rec RECORD;
BEGIN
  SELECT INTO customer_rec * FROM users WHERE user_id=3;
  IF customer_rec.webpage IS NULL THEN
    -- user entered no webpage, return "http://"
    return_webpage = 'http://';
  END IF;
END;
$$ LANGUAGE plpgsql;
```

## No\-op<a name="r_PLpgSQL-no-op"></a>

The no\-op statement \(`NULL;`\) is a placeholder statement that does nothing\. A no\-op statement can indicate that one branch of an IF\-THEN\-ELSE chain is empty\.

```
NULL;
```

## Dynamic SQL<a name="r_PLpgSQL-dynamic-sql"></a>

To generate dynamic commands that can involve different tables or different data types each time they are run from a PL/pgSQL stored procedure, use the `EXECUTE` statement\.

```
EXECUTE command-string [ INTO target ];
```

In the preceding, *command\-string* is an expression yielding a string \(of type text\) that contains the command to be run\. This *command\-string* value is sent to the SQL engine\. No substitution of PL/pgSQL variables is done on the command string\. The values of variables must be inserted in the command string as it is constructed\.

**Note**  
You can't use COMMIT and ROLLBACK statements from within dynamic SQL\. For information about using COMMIT and ROLLBACK statements within a stored procedure, see [Managing transactions](stored-procedure-transaction-management.md)\. 

When working with dynamic commands, you often have to handle escaping of single quotation marks\. We recommend enclosing fixed text in quotation marks in your function body using dollar quoting\. Dynamic values to insert into a constructed query require special handling because they might themselves contain quotation marks\. The following example assumes dollar quoting for the function as a whole, so the quotation marks don't need to be doubled\.

```
EXECUTE 'UPDATE tbl SET '
  || quote_ident(colname)
  || ' = '
  || quote_literal(newvalue)
  || ' WHERE key = '
  || quote_literal(keyvalue);
```

The preceding example shows the functions `quote_ident(text)` and `quote_literal(text)`\. This example passes variables that contain column and table identifiers to the `quote_ident` function\. It also passes variables that contain literal strings in the constructed command to the `quote_literal` function\. Both functions take the appropriate steps to return the input text enclosed in double or single quotation marks respectively, with any embedded special characters properly escaped\.

Dollar quoting is only useful for quoting fixed text\. Don't write the preceding example in the following format\.

```
EXECUTE 'UPDATE tbl SET '
  || quote_ident(colname)
  || ' = $$'
  || newvalue
  || '$$ WHERE key = '
  || quote_literal(keyvalue);
```

You don't do this because the example breaks if the contents of `newvalue` happen to contain $$\. The same problem applies to any other dollar\-quoting delimiter that you might choose\. To safely quote text that is not known in advance, use the `quote_literal` function\.

## Return<a name="r_PLpgSQL-return"></a>

The RETURN statement returns back to the caller from a stored procedure\.

```
RETURN;
```

The following shows an example\.

```
CREATE OR REPLACE PROCEDURE return_example(a int)
AS $$  
BEGIN
  FOR b in 1..10 LOOP
    IF b < a THEN
      RAISE INFO 'b = %', b;
    ELSE
      RETURN;
    END IF;
  END LOOP;
END;
$$ LANGUAGE plpgsql;
```

## Conditionals: IF<a name="r_PLpgSQL-conditionals-if"></a>

The IF conditional statement can take the following forms in the PL/pgSQL language that Amazon Redshift uses:
+ IF \.\.\. THEN

  ```
  IF boolean-expression THEN
    statements
  END IF;
  ```

  The following shows an example\.

  ```
  IF v_user_id <> 0 THEN
    UPDATE users SET email = v_email WHERE user_id = v_user_id;
  END IF;
  ```
+ IF \.\.\. THEN \.\.\. ELSE

  ```
  IF boolean-expression THEN
    statements
  ELSE
    statements
  END IF;
  ```

  The following shows an example\.

  ```
  IF parentid IS NULL OR parentid = ''
  THEN
    return_name = fullname;
    RETURN;
  ELSE
    return_name = hp_true_filename(parentid) || '/' || fullname;
    RETURN;
  END IF;
  ```
+ IF \.\.\. THEN \.\.\. ELSIF \.\.\. THEN \.\.\. ELSE 

  The key word ELSIF can also be spelled ELSEIF\.

  ```
  IF boolean-expression THEN
    statements
  [ ELSIF boolean-expression THEN
    statements
  [ ELSIF boolean-expression THEN
    statements
      ...] ]
  [ ELSE
    statements ]
  END IF;
  ```

  The following shows an example\.

  ```
  IF number = 0 THEN
    result := 'zero';
  ELSIF number > 0 THEN
    result := 'positive';
  ELSIF number < 0 THEN
    result := 'negative';
  ELSE
    -- the only other possibility is that number is null
    result := 'NULL';
  END IF;
  ```

## Conditionals: CASE<a name="r_PLpgSQL-conditionals-case"></a>

The CASE conditional statement can take the following forms in the PL/pgSQL language that Amazon Redshift uses:
+ Simple CASE 

  ```
  CASE search-expression
  WHEN expression [, expression [ ... ]] THEN
    statements
  [ WHEN expression [, expression [ ... ]] THEN
    statements
    ... ]
  [ ELSE
    statements ]
  END CASE;
  ```

  A simple CASE statement provides conditional execution based on equality of operands\.

  The *search\-expression* value is evaluated one time and successively compared to each *expression* in the WHEN clauses\. If a match is found, then the corresponding *statements* run, and then control passes to the next statement after END CASE\. Subsequent WHEN expressions aren't evaluated\. If no match is found, the ELSE *statements* run\. However, if ELSE isn't present, then a CASE\_NOT\_FOUND exception is raised\.

  The following shows an example\.

  ```
  CASE x
  WHEN 1, 2 THEN
    msg := 'one or two';
  ELSE
    msg := 'other value than one or two';
  END CASE;
  ```
+ Searched CASE 

  ```
  CASE
  WHEN boolean-expression THEN
    statements
  [ WHEN boolean-expression THEN
    statements
    ... ]
  [ ELSE
    statements ]
  END CASE;
  ```

  The searched form of CASE provides conditional execution based on truth of Boolean expressions\. 

  Each WHEN clause's *boolean\-expression* is evaluated in turn, until one is found that yields true\. Then the corresponding statements run, and then control passes to the next statement after END CASE\. Subsequent WHEN *expressions* aren't evaluated\. If no true result is found, the ELSE *statements* are run\. However, if ELSE isn't present, then a CASE\_NOT\_FOUND exception is raised\.

  The following shows an example\.

  ```
  CASE
  WHEN x BETWEEN 0 AND 10 THEN
    msg := 'value is between zero and ten';
  WHEN x BETWEEN 11 AND 20 THEN
    msg := 'value is between eleven and twenty';
  END CASE;
  ```

## Loops<a name="r_PLpgSQL-loops"></a>

Loop statements can take the following forms in the PL/pgSQL language that Amazon Redshift uses:
+ Simple loop 

  ```
  [<<label>>]
  LOOP
    statements
  END LOOP [ label ];
  ```

  A simple loop defines an unconditional loop that is repeated indefinitely until terminated by an EXIT or RETURN statement\. The optional label can be used by EXIT and CONTINUE statements within nested loops to specify which loop the EXIT and CONTINUE statements refer to\.

  The following shows an example\.

  ```
  CREATE OR REPLACE PROCEDURE simple_loop()
  LANGUAGE plpgsql
  AS $$
  BEGIN
    <<simple_while>>
    LOOP
      RAISE INFO 'I am raised once';  
      EXIT simple_while;
      RAISE INFO 'I am not raised';
    END LOOP;
    RAISE INFO 'I am raised once as well';
  END;
  $$;
  ```
+ Exit loop

  ```
  EXIT [ label ] [ WHEN expression ];
  ```

  If *label* isn't present, the innermost loop is terminated and the statement following the END LOOP runs next\. If *label* is present, it must be the label of the current or some outer level of nested loop or block\. Then, the named loop or block is terminated and control continues with the statement after the loop or block corresponding END\.

  If WHEN is specified, the loop exit occurs only if *expression* is true\. Otherwise, control passes to the statement after EXIT\.

  You can use EXIT with all types of loops; it isn't limited to use with unconditional loops\.

  When used with a BEGIN block, EXIT passes control to the next statement after the end of the block\. A label must be used for this purpose\. An unlabeled EXIT is never considered to match a BEGIN block\.

  The following shows an example\.

  ```
  CREATE OR REPLACE PROCEDURE simple_loop_when(x int)
  LANGUAGE plpgsql
  AS $$
  DECLARE i INTEGER := 0;
  BEGIN
    <<simple_loop_when>>
    LOOP
      RAISE INFO 'i %', i;
      i := i + 1;
      EXIT simple_loop_when WHEN (i >= x);
    END LOOP;
  END;
  $$;
  ```
+ Continue loop 

  ```
  CONTINUE [ label ] [ WHEN expression ];
  ```

  If *label* is not given, the execution jumps to the next iteration of the innermost loop\. That is, all statements remaining in the loop body are skipped\. Control then returns to the loop control expression \(if any\) to determine whether another loop iteration is needed\. If *label* is present, it specifies the label of the loop whose execution is continued\.

  If WHEN is specified, the next iteration of the loop is begun only if *expression* is true\. Otherwise, control passes to the statement after CONTINUE\.

  You can use CONTINUE with all types of loops; it isn't limited to use with unconditional loops\.

  ```
  CONTINUE mylabel;
  ```
+ WHILE loop 

  ```
  [<<label>>]
  WHILE expression LOOP
    statements
  END LOOP [ label ];
  ```

  The WHILE statement repeats a sequence of statements so long as the *boolean\-expression* evaluates to true\. The expression is checked just before each entry to the loop body\.

  The following shows an example\.

  ```
  WHILE amount_owed > 0 AND gift_certificate_balance > 0 LOOP
    -- some computations here
  END LOOP;
  
  WHILE NOT done LOOP
    -- some computations here
  END LOOP;
  ```
+ FOR loop \(integer variant\) 

  ```
  [<<label>>]
  FOR name IN [ REVERSE ] expression .. expression LOOP
    statements
  END LOOP [ label ];
  ```

  The FOR loop \(integer variant\) creates a loop that iterates over a range of integer values\. The variable name is automatically defined as type integer and exists only inside the loop\. Any existing definition of the variable name is ignored within the loop\. The two expressions giving the lower and upper bound of the range are evaluated one time when entering the loop\.  If you specify REVERSE, then the step value is subtracted, rather than added, after each iteration\.

  If the lower bound is greater than the upper bound \(or less than, in the REVERSE case\), the loop body doesn't run\. No error is raised\.

  If a label is attached to the FOR loop, then you can reference the integer loop variable with a qualified name, using that label\.

  The following shows an example\.

  ```
  FOR i IN 1..10 LOOP
    -- i will take on the values 1,2,3,4,5,6,7,8,9,10 within the loop
  END LOOP;
  
  FOR i IN REVERSE 10..1 LOOP
    -- i will take on the values 10,9,8,7,6,5,4,3,2,1 within the loop
  END LOOP;
  ```
+ FOR loop \(result set variant\) 

  ```
  [<<label>>]
  FOR target IN query LOOP
    statements
  END LOOP [ label ];
  ```

  The *target* is a record variable or comma\-separated list of scalar variables\. The target is successively assigned each row resulting from the query, and the loop body is run for each row\.

  The FOR loop \(result set variant\) enables a stored procedure to iterate through the results of a query and manipulate that data accordingly\.

  The following shows an example\.

  ```
  CREATE PROCEDURE cs_refresh_reports() AS $$
  DECLARE
    reports RECORD;
  BEGIN
    PERFORM cs_log('Refreshing reports...');
    FOR reports IN SELECT * FROM cs_reports ORDER BY sort_key LOOP
      -- Now "reports" has one record from cs_reports
      PERFORM cs_log('Refreshing report ' || quote_ident(reports.report_name) || ' ...');
      EXECUTE 'TRUNCATE TABLE ' || quote_ident(reports.report_name);
      EXECUTE 'INSERT INTO ' || quote_ident(reports.report_name) || ' ' || reports.report_query;
    END LOOP;
    PERFORM cs_log('Done refreshing reports.');
    RETURN;
  END;
  $$ LANGUAGE plpgsql;
  ```
+ FOR loop with dynamic SQL

  ```
  [<<label>>]
  FOR record_or_row IN EXECUTE text_expression LOOP 
    statements
  END LOOP;
  ```

  A FOR loop with dynamic SQL enables a stored procedure to iterate through the results of a dynamic query and manipulate that data accordingly\.

  The following shows an example\.

  ```
  CREATE OR REPLACE PROCEDURE for_loop_dynamic_sql(x int)
  LANGUAGE plpgsql
  AS $$
  DECLARE
    rec RECORD;
    query text;
  BEGIN
    query := 'SELECT * FROM tbl_dynamic_sql LIMIT ' || x;
    FOR rec IN EXECUTE query
    LOOP
      RAISE INFO 'a %', rec.a;
    END LOOP;
  END;
  $$;
  ```

## Cursors<a name="r_PLpgSQL-cursors"></a>

Rather than running a whole query at once, you can set up a cursor\. A *cursor *encapsulates a query and reads the query result a few rows at a time\. One reason for doing this is to avoid memory overrun when the result contains a large number of rows\. Another reason is to return a reference to a cursor that a stored procedure has created, which allows the caller to read the rows\. This approach provides an efficient way to return large row sets from stored procedures\.

To set up a cursor, first you declare a cursor variable\. All access to cursors in PL/pgSQL goes through cursor variables, which are always of the special data type `refcursor`\. A `refcursor` data type simply holds a reference to a cursor\. 

You can create a cursor variable by declaring it as a variable of type `refcursor`\. Or, you can use the cursor declaration syntax following\.

```
name CURSOR [ ( arguments ) ] FOR query ;
```

In the preceding, *arguments* \(if specified\) is a comma\-separated list of *name datatype* pairs that each define names to be replaced by parameter values in *query*\. The actual values to substitute for these names are specified later, when the cursor is opened\.

The following shows examples\.

```
DECLARE
  curs1 refcursor;
  curs2 CURSOR FOR SELECT * FROM tenk1;
  curs3 CURSOR (key integer) IS SELECT * FROM tenk1 WHERE unique1 = key;
```

All three of these variables have the data type `refcursor`, but the first can be used with any query\. In contrast, the second has a fully specified query already bound to it, and the last has a parameterized query bound to it\. The `key` value is replaced by an integer parameter value when the cursor is opened\. The variable `curs1` is said to be *unbound *because it is not bound to any particular query\.

Before you can use a cursor to retrieve rows, it must be opened\. PL/pgSQL has three forms of the OPEN statement, of which two use unbound cursor variables and the third uses a bound cursor variable:
+ Open for select: The cursor variable is opened and given the specified query to run\. The cursor can't be open already\. Also, it must have been declared as an unbound cursor \(that is, as a simple `refcursor` variable\)\. The SELECT query is treated in the same way as other SELECT statements in PL/pgSQL\. 

  ```
  OPEN cursor_name FOR SELECT ...;                     
  ```

  The following shows an example\.

  ```
  OPEN curs1 FOR SELECT * FROM foo WHERE key = mykey;    
  ```
+ Open for execute: The cursor variable is opened and given the specified query to run\. The cursor can't be open already\. Also, it must have been declared as an unbound cursor \(that is, as a simple `refcursor` variable\)\. The query is specified as a string expression in the same way as in the EXECUTE command\. This approach gives flexibility so the query can vary from one run to the next\.

  ```
  OPEN cursor_name FOR EXECUTE query_string;
  ```

  The following shows an example\.

  ```
  OPEN curs1 FOR EXECUTE ’SELECT * FROM ’ || quote_ident($1);
  ```
+ Open a bound cursor: This form of OPEN is used to open a cursor variable whose query was bound to it when it was declared\. The cursor can't be open already\. A list of actual argument value expressions must appear if and only if the cursor was declared to take arguments\. These values are substituted in the query\. 

  ```
  OPEN bound_cursor_name [ ( argument_values ) ];
  ```

  The following shows an example\.

  ```
  OPEN curs2;
  OPEN curs3(42);
  ```

After a cursor has been opened, you can work with it by using the statements described following\. These statements don't have to occur in the same stored procedure that opened the cursor\. You can return a `refcursor` value out of a stored procedure and let the caller operate on the cursor\. All portals are implicitly closed at transaction end\. Thus, you can use a `refcursor` value to reference an open cursor only until the end of the transaction\.
+ FETCH retrieves the next row from the cursor into a target\. This target can be a row variable, a record variable, or a comma\-separated list of simple variables, just as with SELECT INTO\. As with SELECT INTO, you can check the special variable FOUND to see whether a row was obtained\.

  ```
  FETCH cursor INTO target;
  ```

  The following shows an example\.

  ```
  FETCH curs1 INTO rowvar;
  ```
+ CLOSE closes the portal underlying an open cursor\. You can use this statement to release resources earlier than end of the transaction\. You can also use this statement to free the cursor variable to be opened again\.

  ```
  CLOSE cursor;
  ```

  The following shows an example\.

  ```
  CLOSE curs1;
  ```

## RAISE<a name="r_PLpgSQL-messages-errors"></a>

Use the RAISE statement to report messages and raise errors\.

```
RAISE level 'format' [, variable [, ...]];
```

Possible levels are NOTICE, INFO, LOG, WARNING, and EXCEPTION\. EXCEPTION raises an error, which normally aborts the current transaction\. The other levels generate only messages of different priority levels\. 

Inside the format string, % is replaced by the next optional argument's string representation\. Write %% to emit a literal %\. Currently, optional arguments must be simple variables, not expressions, and the format must be a simple string literal\.

In the following example, the value of `v_job_id` replaces the % in the string\.

```
RAISE NOTICE ’Calling cs_create_job(%)’, v_job_id;
```

## Transaction control<a name="r_PLpgSQL-transaction-control"></a>

You can work with transaction control statements in the PL/pgSQL language that Amazon Redshift uses\. For information about using the statements COMMIT, ROLLBACK, and TRUNCATE within a stored procedure, see [Managing transactions](stored-procedure-transaction-management.md)\. 