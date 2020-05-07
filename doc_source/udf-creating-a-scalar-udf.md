# Creating a scalar Python UDF<a name="udf-creating-a-scalar-udf"></a>

A scalar Python UDF incorporates a Python program that executes when the function is called and returns a single value\. The [CREATE FUNCTION](r_CREATE_FUNCTION.md) command defines the following parameters:
+ \(Optional\) Input arguments\. Each argument must have a name and a data type\. 
+ One return data type\.
+ One executable Python program\.

The input and return data types can be SMALLINT, INTEGER, BIGINT, DECIMAL, REAL, DOUBLE PRECISION, BOOLEAN, CHAR, VARCHAR, DATE, or TIMESTAMP\.  In addition, Python UDFs can use the data type ANYELEMENT, which Amazon Redshift automatically converts to a standard data type based on the arguments supplied at run time\. For more information, see [ANYELEMENT data type](#udf-anyelement-data-type)

When an Amazon Redshift query calls a scalar UDF, the following steps occur at run time\.

1. The function converts the input arguments to Python data types\.

   For a mapping of Amazon Redshift data types to Python data types, see [Python UDF data types](udf-data-types.md)\.

1. The function executes the Python program, passing the converted input arguments\.

1. The Python code returns a single value\. The data type of the return value must correspond to the RETURNS data type specified by the function definition\.

1. The function converts the Python return value to the specified Amazon Redshift data type, then returns that value to the query\.

## Scalar Python UDF example<a name="udf-scalar-function-example"></a>

The following example creates a function that compares two numbers and returns the larger value\. Note that the indentation of the code between the double dollar signs \($$\) is a Python requirement\. For more information, see [CREATE FUNCTION](r_CREATE_FUNCTION.md)\.

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

The following query calls the new `f_greater` function to query the SALES table and return either COMMISSION or 20 percent of PRICEPAID, whichever is greater\.

```
select f_py_greater (commission, pricepaid*0.20) from sales;
```

## ANYELEMENT data type<a name="udf-anyelement-data-type"></a>

ANYELEMENT is a *polymorphic data type*, which means that if a function is declared using ANYELEMENT for an argument's data type, the function can accept any standard Amazon Redshift data type as input for that argument when the function is called\. The ANYELEMENT argument is set to the data type actually passed to it when the function is called\.

If a function uses multiple ANYELEMENT data types, they must all resolve to the same actual data type when the function is called\. All ANYELEMENT argument data types are set to the actual data type of the first argument passed to an ANYELEMENT\. For example, a function declared as `f_equal(anyelement, anyelement)` will take any two input values, so long as they are of the same data type\.

If the return value of a function is declared as ANYELEMENT, at least one input argument must be ANYELEMENT\. The actual data type for the return value will be the same as the actual data type supplied for the ANYELEMENT input argument\. 