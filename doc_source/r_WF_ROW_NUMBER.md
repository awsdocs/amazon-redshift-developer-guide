# ROW\_NUMBER window function<a name="r_WF_ROW_NUMBER"></a>

Determines the ordinal number of the current row within a group of rows, counting from 1, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the ordinal numbers are reset for each group of rows\. Rows with equal values for the ORDER BY expressions receive the different row numbers nondeterministically\. 

## Syntax<a name="r_WF_ROW_NUMBER-synopsis"></a>

```
ROW_NUMBER () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_ROW_NUMBER-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the ROW\_NUMBER function\. 

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the ROW\_NUMBER function\. 

ORDER BY *order\_list*   
Optional\. The expression that defines the columns on which the row numbers are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\.    
If ORDER BY does not produce a unique ordering or is omitted, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="c_Supported_data_types_r_WF_ROW_NUMBER"></a>

BIGINT

## Examples<a name="r_WF_ROW_NUMBER-examples"></a>

See [ROW\_NUMBER window function examples](r_Examples_of_WF_ROW_NUMBER_WF.md)\. 