# RANK Window Function<a name="r_WF_RANK"></a>

 The RANK window function determines the rank of a value in a group of values, based on the ORDER BY expression in the OVER clause\. If the optional PARTITION BY clause is present, the rankings are reset for each group of rows\. Rows with equal values for the ranking criteria receive the same rank\. Amazon Redshift adds the number of tied rows to the tied rank to calculate the next rank and thus the ranks might not be consecutive numbers\. For example, if two rows are ranked 1, the next rank is 3\. 

 RANK differs from the [DENSE\_RANK Window Function](r_WF_DENSE_RANK.md) in one respect: For DENSE\_RANK, if two or more rows tie, there is no gap in the sequence of ranked values\. For example, if two rows are ranked 1, the next rank is 2\.

You can have ranking functions with different PARTITION BY and ORDER BY clauses in the same query\. 

## Syntax<a name="r_WF_RANK-synopsis"></a>

```
RANK () OVER
(
[ PARTITION BY expr_list ]
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_RANK-arguments"></a>

\( \)   
The function takes no arguments, but the empty parentheses are required\. 

OVER   
The window clauses for the RANK function\.

PARTITION BY *expr\_list*   
Optional\. One or more expressions that define the window\. 

ORDER BY *order\_list*   
Optional\. Defines the columns on which the ranking values are based\. If no PARTITION BY is specified, ORDER BY uses the entire table\. If ORDER BY is omitted, the return value is 1 for all rows\.   
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique Ordering of Data for Window Functions](r_Examples_order_by_WF.md)\. 

## Return Type<a name="c_Supported_data_types_wf_rank"></a>

INTEGER

## Examples<a name="r_WF_RANK-examples"></a>

See [RANK Window Function Examples](r_Examples_of_rank_WF.md)\. 