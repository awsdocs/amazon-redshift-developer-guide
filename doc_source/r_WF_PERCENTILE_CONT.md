# PERCENTILE\_CONT window function<a name="r_WF_PERCENTILE_CONT"></a>

PERCENTILE\_CONT is an inverse distribution function that assumes a continuous distribution model\. It takes a percentile value and a sort specification, and returns an interpolated value that would fall into the given percentile value with respect to the sort specification\. 

PERCENTILE\_CONT computes a linear interpolation between values after ordering them\. Using the percentile value `(P)` and the number of not null rows `(N)` in the aggregation group, the function computes the row number after ordering the rows according to the sort specification\. This row number `(RN)` is computed according to the formula `RN = (1+ (P*(N-1))`\. The final result of the aggregate function is computed by linear interpolation between the values from rows at row numbers `CRN = CEILING(RN)` and `FRN = FLOOR(RN)`\. 

The final result will be as follows\.

If `(CRN = FRN = RN)` then the result is `(value of expression from row at RN)` 

Otherwise the result is as follows:

`(CRN - RN) * (value of expression for row at FRN) + (RN - FRN) * (value of expression for row at CRN)`\.

You can specify only the PARTITION clause in the OVER clause\. If PARTITION is specified, for each row, PERCENTILE\_CONT returns the value that would fall into the specified percentile among a set of values within a given partition\. 

PERCENTILE\_CONT is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_WF_PERCENTILE_CONT-synopsis"></a>

```
PERCENTILE_CONT ( percentile )
WITHIN GROUP (ORDER BY expr)
OVER (  [ PARTITION BY expr_list ]  )
```

## Arguments<a name="r_WF_PERCENTILE_CONT-arguments"></a>

 *percentile*   
Numeric constant between 0 and 1\. Nulls are ignored in the calculation\.

WITHIN GROUP \( ORDER BY *expr*\)   
Specifies numeric or date/time values to sort and compute the percentile over\. 

OVER   
Specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

PARTITION BY *expr*   
Optional argument that sets the range of records for each group in the OVER clause\.

## Returns<a name="r_WF_PERCENTILE_CONT-returns"></a>

The return type is determined by the data type of the ORDER BY expression in the WITHIN GROUP clause\. The following table shows the return type for each ORDER BY expression data type\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_WF_PERCENTILE_CONT.html)

## Usage notes<a name="r_WF_PERCENTILE_CONT-usage-notes"></a>

If the ORDER BY expression is a DECIMAL data type defined with the maximum precision of 38 digits, it is possible that PERCENTILE\_CONT will return either an inaccurate result or an error\. If the return value of the PERCENTILE\_CONT function exceeds 38 digits, the result is truncated to fit, which causes a loss of precision\. If, during interpolation, an intermediate result exceeds the maximum precision, a numeric overflow occurs and the function returns an error\. To avoid these conditions, we recommend either using a data type with lower precision or casting the ORDER BY expression to a lower precision\. 

For example, a SUM function with a DECIMAL argument returns a default precision of 38 digits\. The scale of the result is the same as the scale of the argument\. So, for example, a SUM of a DECIMAL\(5,2\) column returns a DECIMAL\(38,2\) data type\. 

The following example uses a SUM function in the ORDER BY clause of a PERCENTILE\_CONT function\. The data type of the PRICEPAID column is DECIMAL \(8,2\), so the SUM function returns DECIMAL\(38,2\)\. 

```
select salesid, sum(pricepaid), percentile_cont(0.6) 
within group (order by sum(pricepaid) desc) over()
from sales where salesid < 10 group by salesid;
```

To avoid a potential loss of precision or an overflow error, cast the result to a DECIMAL data type with lower precision, as the following example shows\.

```
select salesid, sum(pricepaid), percentile_cont(0.6) 
within group (order by sum(pricepaid)::decimal(30,2) desc) over()
from sales where salesid < 10 group by salesid;
```

## Examples<a name="r_WF_PERCENTILE_CONT-examples"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 

```
select sellerid, qty, percentile_cont(0.5) 
within group (order by qty) 
over() as median from winsales;

 sellerid | qty | median 
----------+-----+--------
        1 |  10 |   20.0
        1 |  10 |   20.0
        3 |  10 |   20.0
        4 |  10 |   20.0
        3 |  15 |   20.0
        2 |  20 |   20.0
        3 |  20 |   20.0
        2 |  20 |   20.0
        3 |  30 |   20.0
        1 |  30 |   20.0
        4 |  40 |   20.0
(11 rows)
```

```
select sellerid, qty, percentile_cont(0.5) 
within group (order by qty) 
over(partition by sellerid) as median from winsales;

 sellerid | qty | median 
----------+-----+--------
        2 |  20 |   20.0
        2 |  20 |   20.0
        4 |  10 |   25.0
        4 |  40 |   25.0
        1 |  10 |   10.0
        1 |  10 |   10.0
        1 |  30 |   10.0
        3 |  10 |   17.5
        3 |  15 |   17.5
        3 |  20 |   17.5
        3 |  30 |   17.5
(11 rows)
```

The following example calculates the PERCENTILE\_CONT and PERCENTILE\_DISC of the ticket sales for sellers in Washington state\. 

```
SELECT sellerid, state, sum(qtysold*pricepaid) sales, 
percentile_cont(0.6) within group (order by sum(qtysold*pricepaid::decimal(14,2) ) desc) over(),
percentile_disc(0.6) within group (order by sum(qtysold*pricepaid::decimal(14,2) ) desc) over()
from sales s, users u 
where s.sellerid = u.userid and state = 'WA' and sellerid < 1000
group by sellerid, state;

 sellerid | state |  sales  | percentile_cont | percentile_disc
----------+-------+---------+-----------------+-----------------
      127 | WA    | 6076.00 |         2044.20 |         1531.00
      787 | WA    | 6035.00 |         2044.20 |         1531.00
      381 | WA    | 5881.00 |         2044.20 |         1531.00
      777 | WA    | 2814.00 |         2044.20 |         1531.00
       33 | WA    | 1531.00 |         2044.20 |         1531.00
      800 | WA    | 1476.00 |         2044.20 |         1531.00
        1 | WA    | 1177.00 |         2044.20 |         1531.00
(7 rows)
```