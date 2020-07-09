# LAG window function<a name="r_WF_LAG"></a>

 The LAG window function returns the values for a row at a given offset above \(before\) the current row in the partition\. 

## Syntax<a name="r_WF_LAG-synopsis"></a>

```
LAG (value_expr [, offset ])
[ IGNORE NULLS | RESPECT NULLS ]
OVER ( [ PARTITION BY window_partition ] ORDER BY window_ordering )
```

## Arguments<a name="r_WF_LAG-arguments"></a>

 *value\_expr*   
 The target column or expression that the function operates on\. 

 *offset*   
 An optional parameter that specifies the number of rows before the current row to return values for\. The offset can be a constant integer or an expression that evaluates to an integer\. If you do not specify an offset, Amazon Redshift uses `1` as the default value\. An offset of `0` indicates the current row\. 

IGNORE NULLS   
An optional specification that indicates that Amazon Redshift should skip null values in the determination of which row to use\. Null values are included if IGNORE NULLS is not listed\.   
You can use an NVL or COALESCE expression to replace the null values with another value\. For more information, see [NVL expression](r_NVL_function.md)\. 

RESPECT NULLS   
 Indicates that Amazon Redshift should include null values in the determination of which row to use\. RESPECT NULLS is supported by default if you do not specify IGNORE NULLS\. 

OVER   
Specifies the window partitioning and ordering\. The OVER clause cannot contain a window frame specification\. 

PARTITION BY *window\_partition*   
An optional argument that sets the range of records for each group in the OVER clause\. 

ORDER BY *window\_ordering*   
Sorts the rows within each partition\. 

The LAG window function supports expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *value\_expr*\. 

## Examples<a name="r_WF_LAG-examples"></a>

 The following example shows the quantity of tickets sold to the buyer with a buyer ID of 3 and the time that buyer 3 bought the tickets\. To compare each sale with the previous sale for buyer 3, the query returns the previous quantity sold for each sale\. Since there is no purchase before 1/16/2008, the first previous quantity sold value is null: 

```
select buyerid, saletime, qtysold,
lag(qtysold,1) over (order by buyerid, saletime) as prev_qtysold
from sales where buyerid = 3 order by buyerid, saletime;

buyerid |      saletime       | qtysold | prev_qtysold
---------+---------------------+---------+--------------
3 | 2008-01-16 01:06:09 |       1 |
3 | 2008-01-28 02:10:01 |       1 |            1
3 | 2008-03-12 10:39:53 |       1 |            1
3 | 2008-03-13 02:56:07 |       1 |            1
3 | 2008-03-29 08:21:39 |       2 |            1
3 | 2008-04-27 02:39:01 |       1 |            2
3 | 2008-08-16 07:04:37 |       2 |            1
3 | 2008-08-22 11:45:26 |       2 |            2
3 | 2008-09-12 09:11:25 |       1 |            2
3 | 2008-10-01 06:22:37 |       1 |            1
3 | 2008-10-20 01:55:51 |       2 |            1
3 | 2008-10-28 01:30:40 |       1 |            2
(12 rows)
```