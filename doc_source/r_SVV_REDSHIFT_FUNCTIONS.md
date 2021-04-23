# SVV\_REDSHIFT\_FUNCTIONS<a name="r_SVV_REDSHIFT_FUNCTIONS"></a>

Use SVV\_REDSHIFT\_FUNCTIONS to view a list of all functions that a user has access to\. This set of functions includes the functions on the cluster and the functions from datashares provided by remote clusters\.

## Table columns<a name="r_SVV_REDSHIFT_FUNCTIONS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_REDSHIFT_FUNCTIONS.html)

## Sample query<a name="r_SVV_REDSHIFT_FUNCTIONS-sample-query"></a>

The following example returns the output of SVV\_REDSHIFT\_FUNCTIONS\.

```
SELECT *
FROM svv_redshift_functions
WHERE database_name = 'tickit_db'
    AND SCHEMA_NAME = 'public'
ORDER BY function_name
LIMIT 5;

database_name | schema_name |      function_name    |  function_type   |   argument_type  | result_type   
--------------+-------------+-----------------------+------------------+------------------+-------------
   tickit_db  |    public   |     shared_function   | REGULAR FUNCTION | integer, integer |   integer
```