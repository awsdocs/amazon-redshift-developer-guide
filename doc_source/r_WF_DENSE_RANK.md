# DENSE\_RANK Window Function<a name="r_WF_DENSE_RANK"></a>

The DENSE\_RANK window function determines the rank of a value in a group of values, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the rankings are reset for each group of rows\. Rows with equal values for the ranking criteria receive the same rank\. The DENSE\_RANK function differs from RANK in one respect: If two or more rows tie, there is no gap in the sequence of ranked values\. For example, if two rows are ranked 1, the next rank is 2\. 

You can have ranking functions with different PARTITION BY and ORDER BY clauses in the same query\. 

## Syntax<a name="r_WF_DENSE_RANK-synopsis"></a>

```
DENSE_RANK () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_DENSE_RANK-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the DENSE\_RANK function\. 

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the window\. 

ORDER BY *order\_list*   
Optional\. The expression on which the ranking values are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\. If ORDER BY is omitted, the return value is 1 for all rows\.   
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique Ordering of Data for Window Functions](r_Examples_order_by_WF.md)\. 

## Return Type<a name="c_Supported_data_types_wf_dense_rank"></a>

INTEGER

## Examples<a name="r_WF_DENSE_RANK-examples"></a>

See [DENSE\_RANK Window Function Examples](r_Examples_of_dense_rank_WF.md)\. 