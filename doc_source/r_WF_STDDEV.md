# STDDEV\_SAMP and STDDEV\_POP window functions<a name="r_WF_STDDEV"></a>

The STDDEV\_SAMP and STDDEV\_POP window functions return the sample and population standard deviation of a set of numeric values \(integer, decimal, or floating\-point\)\. See also [STDDEV\_SAMP and STDDEV\_POP functions](r_STDDEV_functions.md)\.

STDDEV\_SAMP and STDDEV are synonyms for the same function\.

## Syntax<a name="r_WF_STDDEV-synopsis"></a>

```
STDDEV_SAMP | STDDEV | STDDEV_POP
( [ ALL ] expression ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list 
                        frame_clause ]
)
```

## Arguments<a name="r_WF_STDDEV-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

ALL   
With the argument ALL, the function retains all duplicate values from the expression\. ALL is the default\. DISTINCT is not supported\.

OVER   
Specifies the window clauses for the aggregation functions\. The OVER clause distinguishes window aggregation functions from normal set aggregation functions\.

PARTITION BY *expr\_list*   
Defines the window for the function in terms of one or more expressions\. 

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY is specified, ORDER BY uses the entire table\.

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window function syntax summary](r_Window_function_synopsis.md)\.

## Data types<a name="c_Supported_data_types_wf_stddev"></a>

The argument types supported by the STDDEV functions are SMALLINT, INTEGER, BIGINT, NUMERIC, DECIMAL, REAL, and DOUBLE PRECISION\.

Regardless of the data type of the expression, the return type of a STDDEV function is a double precision number\.

## Examples<a name="r_wf_stddev-examples"></a>

See [STDDEV\_POP and VAR\_POP window function examples](r_Examples_stddev_variance_WF.md)\. 