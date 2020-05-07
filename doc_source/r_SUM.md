# SUM function<a name="r_SUM"></a>

 The SUM function returns the sum of the input column or expression values\. The SUM function works with numeric values and ignores NULL values\. 

## Syntax<a name="r_SUM-synopsis"></a>

```
SUM ( [ DISTINCT | ALL ] expression )
```

## Arguments<a name="r_SUM-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. 

DISTINCT \| ALL   
With the argument DISTINCT, the function eliminates all duplicate values from the specified expression before calculating the sum\. With the argument ALL, the function retains all duplicate values from the expression for calculating the sum\. ALL is the default\. 

## Data types<a name="c_Supported_data_types_sum"></a>

The argument types supported by the SUM function are SMALLINT, INTEGER, BIGINT, NUMERIC, DECIMAL, REAL, and DOUBLE PRECISION\.

The return types supported by the SUM function are 
+ BIGINT for BIGINT, SMALLINT, and INTEGER arguments
+ NUMERIC for NUMERIC arguments
+ DOUBLE PRECISION for floating point arguments

The default precision for a SUM function result with a 64\-bit NUMERIC or DECIMAL argument is 19\. The default precision for a result with a 128\-bit NUMERIC or DECIMAL argument is 38\. The scale of the result is the same as the scale of the argument\. For example, a SUM of a DEC\(5,2\) column returns a DEC\(19,2\) data type\.

## Examples<a name="r_SUM-examples"></a>

 Find the sum of all commissions paid from the SALES table: 

```
select sum(commission) from sales;

sum
-------------
16614814.65
(1 row)
```

Find the number of seats in all venues in the state of Florida: 

```
select sum(venueseats) from venue
where venuestate = 'FL';

sum
--------
250411
(1 row)
```

Find the number of seats sold in May: 

```
select sum(qtysold) from sales, date
where sales.dateid = date.dateid and date.month = 'MAY';

sum
-------
32291
(1 row)
```