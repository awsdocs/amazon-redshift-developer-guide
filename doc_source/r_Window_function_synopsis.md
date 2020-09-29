# Window function syntax summary<a name="r_Window_function_synopsis"></a>

Standard window function syntax is as follows\.

```
function (expression) OVER (
[ PARTITION BY expr_list ]
[ ORDER BY order_list [ frame_clause ] ] )
```

 Here, *function* is one of the functions described in this section and *expr\_list* is as follows\.

```
expression | column_name [, expr_list ]
```

 The *order\_list* is as follows\. 

```
expression | column_name [ ASC | DESC ] 
[ NULLS FIRST | NULLS LAST ]
[, order_list ]
```

 The *frame\_clause* is as follows\. 

```
ROWS
{ UNBOUNDED PRECEDING | unsigned_value PRECEDING | CURRENT ROW } |

{BETWEEN
{ UNBOUNDED PRECEDING | unsigned_value { PRECEDING | FOLLOWING } |
CURRENT ROW}
AND
{ UNBOUNDED FOLLOWING | unsigned_value { PRECEDING | FOLLOWING } |
CURRENT ROW }}
```

**Note**  
STDDEV\_SAMP and VAR\_SAMP are synonyms for STDDEV and VARIANCE, respectively\. 

## Arguments<a name="r_Window_function_synopsis-arguments"></a>

 *function*   
For details, see the individual function descriptions\. 

OVER   
Clause that defines the window specification\. The OVER clause is mandatory for window functions and differentiates window functions from other SQL functions\. 

PARTITION BY *expr\_list*   
\(Optional\) The PARTITION BY clause subdivides the result set into partitions, much like the GROUP BY clause\. If a partition clause is present, the function is calculated for the rows in each partition\. If no partition clause is specified, a single partition contains the entire table, and the function is computed for that complete table\.   
The ranking functions, DENSE\_RANK, NTILE, RANK, and ROW\_NUMBER, require a global comparison of all the rows in the result set\. When a PARTITION BY clause is used, the query optimizer can execute each aggregation in parallel by spreading the workload across multiple slices according to the partitions\. If the PARTITION BY clause is not present, the aggregation step must be executed serially on a single slice, which can have a significant negative impact on performance, especially for large clusters\.  
Amazon Redshift doesn't support string literals in PARTITION BY clauses\.

ORDER BY *order\_list*   
\(Optional\) The window function is applied to the rows within each partition sorted according to the order specification in ORDER BY\. This ORDER BY clause is distinct from and completely unrelated to an ORDER BY clause in a nonwindow function \(outside of the OVER clause\)\. The ORDER BY clause can be used without the PARTITION BY clause\.   
For the ranking functions, the ORDER BY clause identifies the measures for the ranking values\. For aggregation functions, the partitioned rows must be ordered before the aggregate function is computed for each frame\. For more about window function types, see [Window functions](c_Window_functions.md)\.  
Column identifiers or expressions that evaluate to column identifiers are required in the order list\. Neither constants nor constant expressions can be used as substitutes for column names\.   
NULLS values are treated as their own group, sorted and ranked according to the NULLS FIRST or NULLS LAST option\. By default, NULL values are sorted and ranked last in ASC ordering, and sorted and ranked first in DESC ordering\.  
Amazon Redshift doesn't support string literals in ORDER BY clauses\.  
 If the ORDER BY clause is omitted, the order of the rows is nondeterministic\.   
In any parallel system such as Amazon Redshift, when an ORDER BY clause doesn't produce a unique and total ordering of the data, the order of the rows is nondeterministic\. That is, if the ORDER BY expression produces duplicate values \(a partial ordering\), the return order of those rows might vary from one run of Amazon Redshift to the next\. In turn, window functions might return unexpected or inconsistent results\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

 *column\_name*   
Name of a column to be partitioned by or ordered by\. 

ASC \| DESC   
Option that defines the sort order for the expression, as follows:   
+ ASC: ascending \(for example, low to high for numeric values and 'A' to 'Z' for character strings\)\. If no option is specified, data is sorted in ascending order by default\.
+ DESC: descending \(high to low for numeric values; 'Z' to 'A' for strings\)\. 

NULLS FIRST \| NULLS LAST  
Option that specifies whether NULLS should be ordered first, before non\-null values, or last, after non\-null values\. By default, NULLS are sorted and ranked last in ASC ordering, and sorted and ranked first in DESC ordering\.

 *frame\_clause*   
For aggregate functions, the frame clause further refines the set of rows in a function's window when using ORDER BY\. It enables you to include or exclude sets of rows within the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\.  
The frame clause doesn't apply to ranking functions\. Also, the frame clause isn't required when no ORDER BY clause is used in the OVER clause for an aggregate function\. If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\.  
When no ORDER BY clause is specified, the implied frame is unbounded, equivalent to ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING\. 

ROWS  
This clause defines the window frame by specifying a physical offset from the current row\.  
This clause specifies the rows in the current window or partition that the value in the current row is to be combined with\. It uses arguments that specify row position, which can be before or after the current row\. The reference point for all window frames is the current row\. Each row becomes the current row in turn as the window frame slides forward in the partition\.  
The frame can be a simple set of rows up to and including the current row\.  

```
{UNBOUNDED PRECEDING | offset PRECEDING | CURRENT ROW}
```
Or it can be a set of rows between two boundaries\.  

```
BETWEEN
{UNBOUNDED PRECEDING | offset { PRECEDING | FOLLOWING }
| CURRENT ROW}
AND
{UNBOUNDED FOLLOWING | offset { PRECEDING | FOLLOWING }
| CURRENT ROW}
```
UNBOUNDED PRECEDING indicates that the window starts at the first row of the partition; *offset* PRECEDING indicates that the window starts a number of rows equivalent to the value of offset before the current row\. UNBOUNDED PRECEDING is the default\.  
CURRENT ROW indicates the window begins or ends at the current row\.   
UNBOUNDED FOLLOWING indicates that the window ends at the last row of the partition; *offset* FOLLOWING indicates that the window ends a number of rows equivalent to the value of offset after the current row\.  
*offset* identifies a physical number of rows before or after the current row\. In this case, *offset* must be a constant that evaluates to a positive numeric value\. For example, 5 FOLLOWING ends the frame five rows after the current row\.  
Where BETWEEN is not specified, the frame is implicitly bounded by the current row\. For example, `ROWS 5 PRECEDING` is equal to `ROWS BETWEEN 5 PRECEDING AND CURRENT ROW`\. Also, `ROWS UNBOUNDED FOLLOWING` is equal to `ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING`\.  
You can't specify a frame in which the starting boundary is greater than the ending boundary\. For example, you can't specify any of the following frames\.   

```
between 5 following and 5 preceding
between current row and 2 preceding
between 3 following and current row
```