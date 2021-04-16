# AVG function<a name="r_AVG"></a>

 The AVG function returns the average \(arithmetic mean\) of the input expression values\. The AVG function works with numeric values and ignores NULL values\.

## Syntax<a name="r_AVG-synopsis"></a>

```
AVG ( [ DISTINCT | ALL ] expression )
```

## Arguments<a name="r_AVG-arguments"></a>

 *expression *   
The target column or expression that the function operates on\.

DISTINCT \| ALL   
With the argument DISTINCT, the function eliminates all duplicate values from the specified expression before calculating the average\. With the argument ALL, the function retains all duplicate values from the expression for calculating the average\. ALL is the default\.

## Data types<a name="r_AVG-data-types"></a>

 The argument types supported by the AVG function are SMALLINT, INTEGER, BIGINT, NUMERIC, DECIMAL, REAL, and DOUBLE PRECISION\.

The return types supported by the AVG function are: 
+ NUMERIC for any integer type argument
+ DOUBLE PRECISION for a floating point argument

The default precision for an AVG function result with a 64\-bit NUMERIC or DECIMAL argument is 19\. The default precision for a result with a 128\-bit NUMERIC or DECIMAL argument is 38\. The scale of the result is the same as the scale of the argument\. For example, an AVG of a DEC\(5,2\) column returns a DEC\(19,2\) data type\.

## Examples<a name="r_AVG-examples"></a>

Find the average quantity sold per transaction from the SALES table: 

```
select avg(qtysold)from sales;

avg
-----
2
(1 row)
```

Find the average total price listed for all listings: 

```
select avg(numtickets*priceperticket) as avg_total_price from listing;

avg_total_price
-----------------
3034.41
(1 row)
```

Find the average price paid, grouped by month in descending order: 

```
select avg(pricepaid) as avg_price, month 
from sales, date
where sales.dateid = date.dateid
group by month
order by avg_price desc;

avg_price | month
-----------+-------
659.34 | MAR
655.06 | APR
645.82 | JAN
643.10 | MAY
642.72 | JUN
642.37 | SEP
640.72 | OCT
640.57 | DEC
635.34 | JUL
635.24 | FEB
634.24 | NOV
632.78 | AUG
(12 rows)
```