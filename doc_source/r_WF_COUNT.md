# COUNT window function<a name="r_WF_COUNT"></a>

 The COUNT window function counts the rows defined by the expression\.

The COUNT function has two variations\. COUNT\(\*\) counts all the rows in the target table whether they include nulls or not\. COUNT\(expression\) computes the number of rows with non\-NULL values in a specific column or expression\.

## Syntax<a name="r_WF_COUNT-synopsis"></a>

```
COUNT ( * | [ ALL ] expression) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list 
                        frame_clause ]
)
```

## Arguments<a name="r_WF_COUNT-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

ALL   
With the argument ALL, the function retains all duplicate values from the expression for counting\. ALL is the default\. DISTINCT is not supported\.

OVER   
Specifies the window clauses for the aggregation functions\. The OVER clause distinguishes window aggregation functions from normal set aggregation functions\.

PARTITION BY *expr\_list*   
Defines the window for the COUNT function in terms of one or more expressions\.

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY is specified, ORDER BY uses the entire table\.

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window function syntax summary](r_Window_function_synopsis.md)\.

## Data types<a name="c_Supported_data_types_wf_count"></a>

The COUNT function supports all argument data types\.

The return type supported by the COUNT function is BIGINT\.

## Examples<a name="r_WF_COUNT-examples"></a>

See [COUNT window function examples](r_Examples_of_count_WF.md)\.