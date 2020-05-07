# MAX window function<a name="r_WF_MAX"></a>

 The MAX window function returns the maximum of the input expression values\. The MAX function works with numeric values and ignores NULL values\. 

## Syntax<a name="r_WF_MAX-synopsis"></a>

```
MAX ( [ ALL ] expression ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list frame_clause ]
)
```

## Arguments<a name="r_WF_MAX-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

ALL   
With the argument ALL, the function retains all duplicate values from the expression\. ALL is the default\. DISTINCT is not supported\.

OVER   
 A clause that specifies the window clauses for the aggregation functions\. The OVER clause distinguishes window aggregation functions from normal set aggregation functions\.

PARTITION BY *expr\_list*   
Defines the window for the MAX function in terms of one or more expressions\.

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY is specified, ORDER BY uses the entire table\.

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window function syntax summary](r_Window_function_synopsis.md)\.

## Data types<a name="r_WF_MAX-data-types"></a>

Accepts any data type as input\. Returns the same data type as *expression*\.

## Examples<a name="r_WF_MAX-examples"></a>

See [MAX window function examples](r_Examples_of_max_WF.md)\.