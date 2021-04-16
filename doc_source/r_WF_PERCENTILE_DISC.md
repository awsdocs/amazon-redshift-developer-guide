# PERCENTILE\_DISC window function<a name="r_WF_PERCENTILE_DISC"></a>

PERCENTILE\_DISC is an inverse distribution function that assumes a discrete distribution model\. It takes a percentile value and a sort specification and returns an element from the given set\. 

For a given percentile value P, PERCENTILE\_DISC sorts the values of the expression in the ORDER BY clause and returns the value with the smallest cumulative distribution value \(with respect to the same sort specification\) that is greater than or equal to P\. 

You can specify only the PARTITION clause in the OVER clause\. 

PERCENTILE\_DISC is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_WF_PERCENTILE_DISC-synopsis"></a>

```
PERCENTILE_DISC ( percentile )
WITHIN GROUP (ORDER BY expr)
OVER (  [ PARTITION BY expr_list ]  )
```

## Arguments<a name="r_WF_PERCENTILE_DISC-arguments"></a>

 *percentile*   
Numeric constant between 0 and 1\. Nulls are ignored in the calculation\.

WITHIN GROUP \( ORDER BY *expr*\)   
Specifies numeric or date/time values to sort and compute the percentile over\. 

OVER   
Specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

PARTITION BY *expr*   
Optional argument that sets the range of records for each group in the OVER clause\.

## Returns<a name="r_WF_PERCENTILE_DISC-returns"></a>

The same data type as the ORDER BY expression in the WITHIN GROUP clause\.

## Examples<a name="r_WF_PERCENTILE_DISC-examples"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 

```
select sellerid, qty, percentile_disc(0.5) 
within group (order by qty) 
over() as median from winsales;

sellerid | qty | median
----------+-----+--------
        1 |  10 |     20
        3 |  10 |     20
        1 |  10 |     20
        4 |  10 |     20
        3 |  15 |     20
        2 |  20 |     20
        2 |  20 |     20
        3 |  20 |     20
        1 |  30 |     20
        3 |  30 |     20
        4 |  40 |     20
(11 rows)
```

```
select sellerid, qty, percentile_disc(0.5) 
within group (order by qty) 
over(partition by sellerid) as median from winsales;

sellerid | qty | median
----------+-----+--------
        2 |  20 |     20
        2 |  20 |     20
        4 |  10 |     10
        4 |  40 |     10
        1 |  10 |     10
        1 |  10 |     10
        1 |  30 |     10
        3 |  10 |     15
        3 |  15 |     15
        3 |  20 |     15
        3 |  30 |     15
(11 rows) 
```