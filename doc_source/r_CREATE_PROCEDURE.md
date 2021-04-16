# CREATE PROCEDURE<a name="r_CREATE_PROCEDURE"></a>

Creates a new stored procedure or replaces an existing procedure for the current database\.

## Syntax<a name="r_CREATE_PROCEDURE-synopsis"></a>

```
CREATE [ OR REPLACE ] PROCEDURE sp_procedure_name 
  ( [ [ argname ] [ argmode ] argtype [, ...] ] )
AS $$
  procedure_body
$$ LANGUAGE plpgsql 
[ { SECURITY INVOKER | SECURITY DEFINER } ]
[ SET configuration_parameter { TO value | = value } ]
```

## Parameters<a name="r_CREATE_PROCEDURE-parameters"></a>

 OR REPLACE   
A clause that specifies that if a procedure with the same name and input argument data types, or signature, as this one already exists, the existing procedure is replaced\. You can only replace a procedure with a new procedure that defines an identical set of data types\. You must be a superuser or the owner to replace a procedure\.  
If you define a procedure with the same name as an existing procedure, but a different signature, you create a new procedure\. In other words, the procedure name is overloaded\. For more information, see [Overloading procedure names](stored-procedure-naming.md#stored-procedure-overloading-name)\. 

 *sp\_procedure\_name*   
The name of the procedure\. If you specify a schema name \(such as **myschema\.myprocedure**\), the procedure is created in the specified schema\. Otherwise, the procedure is created in the current schema\. For more information about valid names, see [Names and identifiers](r_names.md)\.   
We recommend that you prefix all stored procedure names with `sp_`\. Amazon Redshift reserves the `sp_` prefix for stored procedure names\. By using the `sp_` prefix, you ensure that your stored procedure name doesn't conflict with any existing or future Amazon Redshift built\-in stored procedure or function names\. For more information, see [Naming stored procedures](stored-procedure-naming.md)\.  
You can define more than one procedure with the same name if the data types for the input arguments, or signatures, are different\. In other words, in this case the procedure name is overloaded\. For more information, see [Overloading procedure names](stored-procedure-naming.md#stored-procedure-overloading-name)

*\[argname\] \[ argmode\] argtype*   
A list of argument names, argument modes, and data types\. Only the data type is required\. Name and mode are optional and their position can be switched\.  
The argument mode can be IN, OUT, or INOUT\. The default is IN\.  
You can use OUT and INOUT arguments to return one or more values from a procedure call\. When there are OUT or INOUT arguments, the procedure call returns one result row containing *n* columns, where *n* is the total number of OUT or INOUT arguments\.  
INOUT arguments are input and output arguments at the same time\. *Input arguments* include both IN and INOUT arguments, and *output arguments* include both OUT and INOUT arguments\.  
OUT arguments aren't specified as part of the CALL statement\. Specify INOUT arguments in the stored procedure CALL statement\. INOUT arguments can be useful when passing and returning values from a nested call, and also when returning a `refcursor`\. For more information on `refcursor` types, see [Cursors](c_PLpgSQL-statements.md#r_PLpgSQL-cursors)\.  
The argument data types can be any standard Amazon Redshift data type\. In addition, an argument data type can be `refcursor`\.  
You can specify a maximum of 32 input arguments and 32 output arguments\. 

AS $$ *procedure\_body* $$   
A construct that encloses the procedure to be executed\. The literal keywords AS $$ and $$ are required\.  
Amazon Redshift requires you to enclose the statement in your procedure by using a format called dollar quoting\. Anything within the enclosure is passed exactly as is\. You don't need to escape any special characters because the contents of the string are written literally\.  
With *dollar quoting, *you use a pair of dollar signs \($$\) to signify the start and the end of the statement to run, as shown in the following example\.  

```
$$ my statement $$
```
Optionally, between the dollar signs in each pair, you can specify a string to help identify the statement\. The string that you use must be the same in both the start and the end of the enclosure pairs\. This string is case\-sensitive, and it follows the same constraints as an unquoted identifier except that it can't contain dollar signs\. The following example uses the string test\.  

```
$test$ my statement $test$
```
This syntax is also useful for nested dollar quoting\. For more information about dollar quoting, see "Dollar\-quoted String Constants" under [Lexical Structure](https://www.postgresql.org/docs/9.0/sql-syntax-lexical.html) in the PostgreSQL documentation\.

 *procedure\_body*   
A set of valid PL/pgSQL statements\. PL/pgSQL statements augment SQL commands with procedural constructs, including looping and conditional expressions, to control logical flow\. Most SQL commands can be used in the procedure body, including data modification language \(DML\) such as COPY, UNLOAD and INSERT, and data definition language \(DDL\) such as CREATE TABLE\. For more information, see [PL/pgSQL language reference](c_pl_pgSQL_reference.md)\.  

LANGUAGE *plpgsql*  
A language value\. Specify `plpgsql`\. You must have permission for usage on language to use `plpgsql`\. For more information, see [GRANT](r_GRANT.md)\. 

SECURITY INVOKER \| SECURITY DEFINER  
The security mode for the procedure determines the procedure's access privileges at runtime\. The procedure must have permission to access the underlying database objects\.   
For SECURITY INVOKER mode, the procedure uses the privileges of the user calling the procedure\. The user must have explicit permissions on the underlying database objects\. The default is SECURITY INVOKER\.  
For SECURITY DEFINER mode, the procedure is run using the database privileges as the procedure's owner\. The user calling the procedure needs execute privilege on the procedure, but doesn't need any privileges on the underlying objects\. 

SET configuration\_parameter \{ TO value \| = value \}  
The SET clause causes the specified `configuration_parameter` to be set to the specified value when the procedure is entered\. This clause then restores `configuration_parameter` to its earlier value when the procedure exits\. 

## Examples<a name="r_CREATE_PROCEDURE-examples"></a>

**Note**  
If when running these examples you encounter an error similar to:  

```
ERROR: 42601: [Amazon](500310) unterminated dollar-quoted string at or near "$$
```
See [Overview of stored procedures in Amazon Redshift](stored-procedure-create.md)\. 

The following example creates a procedure with two input parameters\.

```
CREATE OR REPLACE PROCEDURE test_sp1(f1 int, f2 varchar(20))
AS $$
DECLARE
  min_val int;
BEGIN
  DROP TABLE IF EXISTS tmp_tbl;
  CREATE TEMP TABLE tmp_tbl(id int);
  INSERT INTO tmp_tbl values (f1),(10001),(10002);
  SELECT INTO min_val MIN(id) FROM tmp_tbl;
  RAISE INFO 'min_val = %, f2 = %', min_val, f2;
END;
$$ LANGUAGE plpgsql;
```

The following example creates a procedure with one IN parameter, one OUT parameter, and one INOUT parameter\.

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
```