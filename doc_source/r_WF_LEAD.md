# LEAD window function<a name="r_WF_LEAD"></a>

 The LEAD window function returns the values for a row at a given offset below \(after\) the current row in the partition\. 

## Syntax<a name="r_WF_LEAD-synopsis"></a>

```
LEAD (value_expr [, offset ])
[ IGNORE NULLS | RESPECT NULLS ]
OVER ( [ PARTITION BY window_partition ] ORDER BY window_ordering )
```

## Arguments<a name="r_WF_LEAD-arguments"></a>

 *value\_expr*   
The target column or expression that the function operates on\. 

 *offset*   
 An optional parameter that specifies the number of rows below the current row to return values for\. The offset can be a constant integer or an expression that evaluates to an integer\. If you do not specify an offset, Amazon Redshift uses `1` as the default value\. An offset of `0` indicates the current row\. 

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

The LEAD window function supports expressions that use any of the Amazon Redshift data types\. The return type is the same as the type of the *value\_expr*\. 

## Examples<a name="r_WF_LEAD-examples"></a>

 The following example provides the commission for events in the SALES table for which tickets were sold on January 1, 2008 and January 2, 2008 and the commission paid for ticket sales for the subsequent sale\. 

```
select eventid, commission, saletime,
lead(commission, 1) over (order by saletime) as next_comm
from sales where saletime between '2008-01-01 00:00:00' and '2008-01-02 12:59:59'
order by saletime;

eventid | commission |      saletime       | next_comm
---------+------------+---------------------+-----------
6213 |      52.05 | 2008-01-01 01:00:19 |    106.20
7003 |     106.20 | 2008-01-01 02:30:52 |    103.20
8762 |     103.20 | 2008-01-01 03:50:02 |     70.80
1150 |      70.80 | 2008-01-01 06:06:57 |     50.55
1749 |      50.55 | 2008-01-01 07:05:02 |    125.40
8649 |     125.40 | 2008-01-01 07:26:20 |     35.10
2903 |      35.10 | 2008-01-01 09:41:06 |    259.50
6605 |     259.50 | 2008-01-01 12:50:55 |    628.80
6870 |     628.80 | 2008-01-01 12:59:34 |     74.10
6977 |      74.10 | 2008-01-02 01:11:16 |     13.50
4650 |      13.50 | 2008-01-02 01:40:59 |     26.55
4515 |      26.55 | 2008-01-02 01:52:35 |     22.80
5465 |      22.80 | 2008-01-02 02:28:01 |     45.60
5465 |      45.60 | 2008-01-02 02:28:02 |     53.10
7003 |      53.10 | 2008-01-02 02:31:12 |     70.35
4124 |      70.35 | 2008-01-02 03:12:50 |     36.15
1673 |      36.15 | 2008-01-02 03:15:00 |   1300.80
...
(39 rows)
```