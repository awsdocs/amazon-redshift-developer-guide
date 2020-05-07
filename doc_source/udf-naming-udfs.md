# Naming UDFs<a name="udf-naming-udfs"></a>

You can avoid potential conflicts and unexpected results considering your UDF naming conventions before implementation\. Because function names can be overloaded, they can collide with existing and future Amazon Redshift function names\. This topic discusses overloading and presents a strategy for avoiding conflict\.

## Overloading function names<a name="udf-naming-overloading-function-names"></a>

A function is identified by its name and *signature*, which is the number of input arguments and the data types of the arguments\. Two functions in the same schema can have the same name if they have different signatures\. In other words, the function names can be *overloaded*\.

When you execute a query, the query engine determines which function to call based on the number of arguments you provide and the data types of the arguments\. You can use overloading to simulate functions with a variable number of arguments, up to the limit allowed by the [CREATE FUNCTION](r_CREATE_FUNCTION.md) command\. 

## Preventing UDF naming conflicts<a name="udf-naming-preventing-udf-naming-conflicts"></a>

We recommend that you name all UDFs using the prefix `f_`\. Amazon Redshift reserves the `f_` prefix exclusively for UDFs and by prefixing your UDF names with `f_`, you ensure that your UDF name won't conflict with any existing or future Amazon Redshift built\-in SQL function names\. For example, by naming a new UDF `f_sum`, you avoid conflict with the Amazon Redshift SUM function\. Similarly, if you name a new function `f_fibonacci`, you avoid conflict if Amazon Redshift adds a function named FIBONACCI in a future release\.

You can create a UDF with the same name and signature as an existing Amazon Redshift built\-in SQL function without the function name being overloaded if the UDF and the built\-in function exist in different schemas\. Because built\-in functions exist in the system catalog schema, pg\_catalog, you can create a UDF with the same name in another schema, such as public or a user\-defined schema\. When you call a function that is not explicitly qualified with a schema name, Amazon Redshift searches the pg\_catalog schema first by default, so a built\-in function will run before a new UDF with the same name\.

You can change this behavior by setting the search path to place pg\_catalog at the end so that your UDFs take precedence over built\-in functions, but the practice can cause unexpected results\. Adopting a unique naming strategy, such as using the reserved prefix `f_`, is a more reliable practice\. For more information, see [SET](r_SET.md) and [search\_path](r_search_path.md)\.