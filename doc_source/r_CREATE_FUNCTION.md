# CREATE FUNCTION<a name="r_CREATE_FUNCTION"></a>

Creates a new scalar user\-defined function \(UDF\) using either a SQL SELECT clause or a Python program\.

## Syntax<a name="r_CREATE_FUNCTION-synopsis"></a>

```
CREATE [ OR REPLACE ] FUNCTION f_function_name 
( { [py_arg_name  py_arg_data_type |
sql_arg_data_type } [ , ... ] ] )
RETURNS data_type
{ VOLATILE | STABLE | IMMUTABLE }   
AS $$
  { python_program | SELECT_clause }
$$ LANGUAGE { plpythonu | sql }
```

## Parameters<a name="r_CREATE_FUNCTION-parameters"></a>

OR REPLACE  
Specifies that if a function with the same name and input argument data types, or *signature*, as this one already exists, the existing function is replaced\. You can only replace a function with a new function that defines an identical set of data types\. You must be a superuser to replace a function\.  
If you define a function with the same name as an existing function but a different signature, you create a new function\. In other words, the function name is overloaded\. For more information, see [Overloading function names](udf-naming-udfs.md#udf-naming-overloading-function-names)\.

 *f\_function\_name*   
The name of the function\. If you specify a schema name \(such as `myschema.myfunction`\), the function is created using the specified schema\. Otherwise, the function is created in the current schema\. For more information about valid names, see [Names and identifiers](r_names.md)\.  
We recommend that you prefix all UDF names with `f_`\. Amazon Redshift reserves the `f_` prefix for UDF names, so by using the `f_` prefix, you ensure that your UDF name will not conflict with any existing or future Amazon Redshift built\-in SQL function names\. For more information, see [Naming UDFs](udf-naming-udfs.md)\.  
You can define more than one function with the same function name if the data types for the input arguments are different\. In other words, the function name is overloaded\. For more information, see [Overloading function names](udf-naming-udfs.md#udf-naming-overloading-function-names)\.

 *py\_arg\_name py\_arg\_data\_type \| sql\_arg\_data type*   
For a Python UDF, a list of input argument names and data types\. For a SQL UDF, a list of data types, without argument names\. In a Python UDF, refer to arguments using the argument names\. In a SQL UDF, refer to arguments using $1, $2, and so on, based on the order of the arguments in the argument list\.   
For a SQL UDF, the input and return data types can be any standard Amazon Redshift data type\. For a Python UDF, the input and return data types can be SMALLINT, INTEGER, BIGINT, DECIMAL, REAL, DOUBLE PRECISION, BOOLEAN, CHAR, VARCHAR, DATE, or TIMESTAMP\.  In addition, Python user\-defined functions \(UDFs\) support a data type of ANYELEMENT\. This is automatically converted to a standard data type based on the data type of the corresponding argument supplied at runtime\. If multiple arguments use ANYELEMENT, they will all resolve to the same data type at runtime, based on the first ANYELEMENT argument in the list\. For more information, see [Python UDF data types](udf-data-types.md) and [Data types](c_Supported_data_types.md)\.  
You can specify a maximum of 32 arguments\.

 RETURNS *data\_type*   
The data type of the value returned by the function\. The RETURNS data type can be any standard Amazon Redshift data type\. In addition, Python UDFs can use a data type of ANYELEMENT, which is automatically converted to a standard data type based on the argument supplied at runtime\. If you specify ANYELEMENT for the return data type, at least one argument must use ANYELEMENT\. The actual return data type matches the data type supplied for the ANYELEMENT argument when the function is called\. For more information, see [Python UDF data types](udf-data-types.md)\.

 VOLATILE \| STABLE \| IMMUTABLE   
Informs the query optimizer about the volatility of the function\.   
You will get the best optimization if you label your function with the strictest volatility category that is valid for it\. However, if the category is too strict, there is a risk that the optimizer will erroneously skip some calls, resulting in an incorrect result set\. In order of strictness, beginning with the least strict, the volatility categories are as follows:  
+ VOLATILE
+ STABLE
+ IMMUTABLE
VOLATILE  
Given the same arguments, the function can return different results on successive calls, even for the rows in a single statement\. The query optimizer can't make any assumptions about the behavior of a volatile function, so a query that uses a volatile function must reevaluate the function for every input row\.  
STABLE  
Given the same arguments, the function is guaranteed to return the same results for all rows processed within a single statement\. The function can return different results when called in different statements\. This category allows the optimizer to optimize multiple calls of the function within a single statement to a single call for the statement\.   
IMMUTABLE  
Given the same arguments, the function always returns the same result, forever\. When a query calls an `IMMUTABLE` function with constant arguments, the optimizer pre\-evaluates the function\.

AS $$ *statement* $$  
 A construct that encloses the statement to be executed\. The literal keywords `AS $$` and `$$` are required\.   
Amazon Redshift requires you to enclose the statement in your function by using a format called dollar quoting\. Anything within the enclosure is passed exactly as is\. You don't need to escape any special characters because the contents of the string are written literally\.   
 With *dollar quoting, *you use a pair of dollar signs \($$\) to signify the start and the end of the statement to execute, as shown in the following example\.   

```
$$ my statement $$
```
 Optionally, between the dollar signs in each pair, you can specify a string to help identify the statement\. The string that you use must be the same in both the start and the end of the enclosure pairs\. This string is case\-sensitive, and it follows the same constraints as an unquoted identifier except that it can't contain dollar signs\. The following example uses the string `test`\.   

```
$test$ my statement $test$
```
For more information about dollar quoting, see "Dollar\-quoted String Constants" under [ Lexical Structure](https://www.postgresql.org/docs/9.4/static/sql-syntax-lexical.html) in the PostgreSQL documentation\. 

*python\_program*   
A valid executable Python program that returns a value\. The statement that you pass in with the function must conform to indentation requirements as specified in the [Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/#indentation) on the Python website\. For more information, see [Python language support for UDFs](udf-python-language-support.md)\.

*SQL\_clause*   
A SQL SELECT clause\.  
The SELECT clause can't include any of the following types of clauses:  
+ FROM
+ INTO
+ WHERE
+ GROUP BY
+ ORDER BY
+ LIMIT

LANGUAGE \{ plpythonu \| sql \}   
For Python, specify `plpythonu`\. For SQL, specify `sql`\. You must have permission for usage on language for SQL or plpythonu\. For more information, see [UDF security and privileges](udf-security-and-privileges.md)\.

## Usage notes<a name="r_CREATE_FUNCTION-usage-notes"></a>

### Nested functions<a name="r_CREATE_FUNCTION-usage-notes-nested-functions"></a>

You can call another SQL user\-defined function \(UDF\) from within a SQL UDF\. The nested function must exist when you run the CREATE FUNCTION command\. Amazon Redshift doesn't track dependencies for UDFs, so if you drop the nested function, Amazon Redshift doesn't return an error\. However, the UDF will fail if the nested function doesn't exist\. For example, the following function calls the `f_sql_greater `function in the SELECT clause\.

```
create function f_sql_commission (float, float )
  returns float
stable
as $$
  select f_sql_greater ($1, $2)  
$$ language sql;
```

### UDF security and privileges<a name="r_CREATE_FUNCTION-usage-notes-security-and-privileges"></a>

To create a UDF, you must have permission for usage on language for SQL or plpythonu \(Python\)\. By default, USAGE ON LANGUAGE SQL is granted to PUBLIC, However, you must explicitly grant USAGE ON LANGUAGE PLPYTHONU to specific users or groups\. 

To revoke usage for SQL, first revoke usage from PUBLIC\. Then grant usage on SQL only to the specific users or groups permitted to create SQL UDFs\. The following example revokes usage on SQL from PUBLIC then grants usage to the user group `udf_devs`\.

```
revoke usage on language sql from PUBLIC;
grant usage on language sql to group udf_devs;
```

To execute a UDF, you must have execute permission for each function\. By default, execute permission for new UDFs is granted to PUBLIC\. To restrict usage, revoke execute from PUBLIC for the function\. Then grant the privilege to specific individuals or groups\. 

The following example revokes execution on function `f_py_greater` from PUBLIC then grants usage to the user group `udf_devs`\.

```
revoke execute on function f_py_greater(a float, b float) from PUBLIC;
grant execute on function f_py_greater(a float, b float) to group udf_devs;
```

Superusers have all privileges by default\. 

For more information, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\.

## Examples<a name="r_CREATE_FUNCTION-examples"></a>

### Scalar Python UDF example<a name="r_CREATE_FUNCTION-python-example"></a>

The following example creates a Python UDF that compares two integers and returns the larger value\.

```
create function f_py_greater (a float, b float)
  returns float
stable
as $$
  if a > b:
    return a
  return b
$$ language plpythonu;
```

The following example queries the SALES table and calls the new `f_py_greater` function to return either COMMISSION or 20 percent of PRICEPAID, whichever is greater\.

```
select f_py_greater (commission, pricepaid*0.20) from sales;
```

### Scalar SQL UDF example<a name="r_CREATE_FUNCTION-sql-example"></a>

The following example creates a function that compares two numbers and returns the larger value\. 

```
create function f_sql_greater (float, float)
  returns float
stable
as $$
  select case when $1 > $2 then $1
    else $2
  end
$$ language sql;
```

The following query calls the new `f_sql_greater` function to query the SALES table and returns either COMMISSION or 20 percent of PRICEPAID, whichever is greater\.

```
select f_sql_greater (commission, pricepaid*0.20) from sales;
```