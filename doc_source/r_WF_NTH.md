# NTH\_VALUE Window Function<a name="r_WF_NTH"></a>

 The NTH\_VALUE window function returns the expression value of the specified row of the window frame relative to the first row of the window\. 

## Syntax<a name="r_WF_NTH-synopsis"></a>

```
NTH_VALUE (expr, offset)
[ IGNORE NULLS | RESPECT NULLS ]
OVER
( [ PARTITION BY window_partition ]
[ ORDER BY window_ordering 
                        frame_clause ] )
```

## Arguments<a name="r_WF_NTH-arguments"></a>

 *expr*   
 The target column or expression that the function operates on\. 

 *offset*   
 Determines the row number relative to the first row in the window for which to return the expression\. The *offset* can be a constant or an expression and must be a positive integer that is greater than 0\. 

IGNORE NULLS   
An optional specification that indicates that Amazon Redshift should skip null values in the determination of which row to use\. Null values are included if IGNORE NULLS is not listed\. 

RESPECT NULLS   
 Indicates that Amazon Redshift should include null values in the determination of which row to use\. RESPECT NULLS is supported by default if you do not specify IGNORE NULLS\. 

OVER   
Specifies the window partitioning, ordering, and window frame\. 

PARTITION BY *window\_partition*   
Sets the range of records for each group in the OVER clause\. 

ORDER BY *window\_ordering*   
Sorts the rows within each partition\. If ORDER BY is omitted, the default frame consists of all rows in the partition\. 

 *frame\_clause*   
If an ORDER BY clause is used for an aggregate function, an explicit frame clause is required\. The frame clause refines the set of rows in a function's window, including or excluding sets of rows in the ordered result\. The frame clause consists of the ROWS keyword and associated specifiers\. See [Window Function Syntax Summary](r_Window_function_synopsis.md)\. 

The NTH\_VALUE window function supports expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *expr*\. 

## Examples<a name="r_WF_NTH-examples"></a>

See [NTH\_VALUE Window Function Examples](r_Examples_of_NTH_WF.md)\. 