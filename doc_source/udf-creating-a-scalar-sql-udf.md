# Creating a scalar SQL UDF<a name="udf-creating-a-scalar-sql-udf"></a>

A scalar SQL UDF incorporates a SQL SELECT clause that executes when the function is called and returns a single value\. The [CREATE FUNCTION](r_CREATE_FUNCTION.md) command defines the following parameters:
+ \(Optional\) Input arguments\. Each argument must have a data type\. 
+ One return data type\.
+ One SQL SELECT clause\. In the SELECT clause, refer to the input arguments using $1, $2, and so on, according to the order of the arguments in the function definition\. 

The input and return data types can be any standard Amazon Redshift data type\.

Don't include a FROM clause in your SELECT clause\. Instead, include the FROM clause in the SQL statement that calls the SQL UDF\. 

The SELECT clause can't include any of the following types of clauses:
+ FROM
+ INTO
+ WHERE
+ GROUP BY
+ ORDER BY
+ LIMIT

## Scalar SQL function example<a name="udf-scalar-sql-function-example"></a>

The following example creates a function that compares two numbers and returns the larger value\. For more information, see [CREATE FUNCTION](r_CREATE_FUNCTION.md)\.

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

The following query calls the new f\_sql\_greater function to query the SALES table and return either COMMISSION or 20 percent of PRICEPAID, whichever is greater\.

```
select f_sql_greater(commission, pricepaid*0.20) from sales;
```