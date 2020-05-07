# VAR\_SAMP and VAR\_POP window functions<a name="r_WF_VARIANCE"></a>

 The VAR\_SAMP and VAR\_POP window functions return the sample and population variance of a set of numeric values \(integer, decimal, or floating\-point\)\. See also [VAR\_SAMP and VAR\_POP functions](r_VARIANCE_functions.md)\.

VAR\_SAMP and VARIANCE are synonyms for the same function\. 

## Syntax<a name="r_WF_VARIANCE-synopsis"></a>

```
VAR_SAMP | VARIANCE | VAR_POP
( [ ALL ] expression ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list 
                        frame_clause ]
)
```

## Arguments<a name="r_WF_VARIANCE-arguments"></a>

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

## Data types<a name="c_Supported_data_types_wf_variance"></a>

The argument types supported by the VARIANCE functions are SMALLINT, INTEGER, BIGINT, NUMERIC, DECIMAL, REAL, and DOUBLE PRECISION\.

Regardless of the data type of the expression, the return type of a VARIANCE function is a double precision number\.