# SUM Window Function<a name="r_WF_SUM"></a>

 The SUM window function returns the sum of the input column or expression values\. The SUM function works with numeric values and ignores NULL values\.

## Syntax<a name="r_WF_SUM-synopsis"></a>

```
SUM ( [ ALL ] expression ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list 
                        frame_clause ]
)
```

## Arguments<a name="r_WF_SUM-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

ALL   
With the argument ALL, the function retains all duplicate values from the expression\. ALL is the default\. DISTINCT is not supported\.

OVER   
Specifies the window clauses for the aggregation functions\. The OVER clause distinguishes window aggregation functions from normal set aggregation functions\.

PARTITION BY *expr\_list*   
Defines the window for the SUM function in terms of one or more expressions\.

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY is specified, ORDER BY uses the entire table\.

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window Function Syntax Summary](r_Window_function_synopsis.md)\.

## Data Types<a name="c_Supported_data_types_wf_sum"></a>

The argument types supported by the SUM function are SMALLINT, INTEGER, BIGINT, NUMERIC, DECIMAL, REAL, and DOUBLE PRECISION\.

The return types supported by the SUM function are: 

+ BIGINT for SMALLINT or INTEGER arguments

+ NUMERIC for BIGINT arguments

+ DOUBLE PRECISION for floating\-point arguments

## Examples<a name="r_WF_SUM-examples"></a>

See [SUM Window Function Examples](r_Examples_of_sum_WF.md)\.