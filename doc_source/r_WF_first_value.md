# FIRST\_VALUE and LAST\_VALUE Window Functions<a name="r_WF_first_value"></a>

 Given an ordered set of rows, FIRST\_VALUE returns the value of the specified expression with respect to the first row in the window frame\. The LAST\_VALUE function returns the value of the expression with respect to the last row in the frame\. 

## Syntax<a name="r_WF_first_value-synopsis"></a>

```
FIRST_VALUE | LAST_VALUE
( expression [ IGNORE NULLS | RESPECT NULLS ] ) OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list frame_clause ]
)
```

## Arguments<a name="r_WF_first_value-arguments"></a>

 *expression*   
 The target column or expression that the function operates on\. 

IGNORE NULLS   
When this option is used with FIRST\_VALUE, the function returns the first value in the frame that is not NULL \(or NULL if all values are NULL\)\. When this option is used with LAST\_VALUE, the function returns the last value in the frame that is not NULL \(or NULL if all values are NULL\)\. 

RESPECT NULLS   
 Indicates that Amazon Redshift should include null values in the determination of which row to use\. RESPECT NULLS is supported by default if you do not specify IGNORE NULLS\. 

OVER   
Introduces the window clauses for the function\. 

PARTITION BY *expr\_list*   
Defines the window for the function in terms of one or more expressions\. 

ORDER BY *order\_list*   
Sorts the rows within each partition\. If no PARTITION BY clause is specified, ORDER BY sorts the entire table\. If you specify an ORDER BY clause, you must also specify a *frame\_clause*\.   
The results of the FIRST\_VALUE and LAST\_VALUE functions depend on the ordering of the data\. The results are nondeterministic in the following cases:   

+ When no ORDER BY clause is specified and a partition contains two different values for an expression 

+ When the expression evaluates to different values that correspond to the same value in the ORDER BY list\. 

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows in the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window Function Syntax Summary](r_Window_function_synopsis.md)\. 

## Data Types<a name="c_Supported_data_types_wf_first_value"></a>

These functions support expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *expression*\.

## Examples<a name="r_WF_first_value-examples"></a>

See [FIRST\_VALUE and LAST\_VALUE Window Function Examples](r_Examples_of_firstlast_WF.md)\. 