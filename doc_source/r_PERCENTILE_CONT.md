# PERCENTILE\_CONT function<a name="r_PERCENTILE_CONT"></a>

PERCENTILE\_CONT is an inverse distribution function that assumes a continuous distribution model\. It takes a percentile value and a sort specification, and returns an interpolated value that would fall into the given percentile value with respect to the sort specification\. 

PERCENTILE\_CONT computes a linear interpolation between values after ordering them\. Using the percentile value `(P)` and the number of not null rows `(N)` in the aggregation group, the function computes the row number after ordering the rows according to the sort specification\. This row number `(RN)` is computed according to the formula `RN = (1+ (P*(N-1))`\. The final result of the aggregate function is computed by linear interpolation between the values from rows at row numbers `CRN = CEILING(RN)` and `FRN = FLOOR(RN)`\. 

The final result will be as follows\.

If `(CRN = FRN = RN)` then the result is `(value of expression from row at RN)` 

Otherwise the result is as follows:

`(CRN - RN) * (value of expression for row at FRN) + (RN - FRN) * (value of expression for row at CRN)`\.

PERCENTILE\_CONT is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_PERCENTILE_CONT-synopsis"></a>

```
PERCENTILE_CONT ( percentile )
WITHIN GROUP (ORDER BY expr)
```

## Arguments<a name="r_PERCENTILE_CONT-arguments"></a>

 *percentile*   
Numeric constant between 0 and 1\. Nulls are ignored in the calculation\.

WITHIN GROUP \( ORDER BY *expr*\)   
Specifies numeric or date/time values to sort and compute the percentile over\. 

## Returns<a name="r_PERCENTILE_CONT-returns"></a>

The return type is determined by the data type of the ORDER BY expression in the WITHIN GROUP clause\. The following table shows the return type for each ORDER BY expression data type\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PERCENTILE_CONT.html)

## Usage notes<a name="r_PERCENTILE_CONT-usage-notes"></a>

If the ORDER BY expression is a DECIMAL data type defined with the maximum precision of 38 digits, it is possible that PERCENTILE\_CONT will return either an inaccurate result or an error\. If the return value of the PERCENTILE\_CONT function exceeds 38 digits, the result is truncated to fit, which causes a loss of precision\.\. If, during interpolation, an intermediate result exceeds the maximum precision, a numeric overflow occurs and the function returns an error\. To avoid these conditions, we recommend either using a data type with lower precision or casting the ORDER BY expression to a lower precision\.

If a statement includes multiple calls to sort\-based aggregate functions \(LISTAGG, PERCENTILE\_CONT, or MEDIAN\), they must all use the same ORDER BY values\. Note that MEDIAN applies an implicit order by on the expression value\. 

For example, the following statement returns an error\. 

```
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (pricepaid)
from sales group by salesid, pricepaid;

An error occurred when executing the SQL command:
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (pricepaid)
from sales group by salesid, pricepai...

ERROR: within group ORDER BY clauses for aggregate functions must be the same
```

The following statement executes successfully\. 

```
select top 10 salesid, sum(pricepaid), 
percentile_cont(0.6) within group (order by salesid),
median (salesid)
from sales group by salesid, pricepaid;
```

## Examples<a name="r_PERCENTILE_CONT-examples"></a>

The following example shows that MEDIAN produces the same results as PERCENTILE\_CONT\(0\.5\)\. 

```
select top 10  distinct sellerid, qtysold, 
percentile_cont(0.5) within group (order by qtysold),
median (qtysold) 
from sales
group by sellerid, qtysold;

sellerid | qtysold | percentile_cont | median
---------+---------+-----------------+-------
       1 |       1 |             1.0 |    1.0
       2 |       3 |             3.0 |    3.0
       5 |       2 |             2.0 |    2.0
       9 |       4 |             4.0 |    4.0
      12 |       1 |             1.0 |    1.0
      16 |       1 |             1.0 |    1.0
      19 |       2 |             2.0 |    2.0
      19 |       3 |             3.0 |    3.0
      22 |       2 |             2.0 |    2.0
      25 |       2 |             2.0 |    2.0
```