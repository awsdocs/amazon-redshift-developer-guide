# POWER function<a name="r_POWER"></a>

## Syntax<a name="r_POWER-synopsis"></a>

 The POWER function is an exponential function that raises a numeric expression to the power of a second numeric expression\. For example, 2 to the third power is calculated as `power(2,3)`, with a result of 8\. 

```
POW | POWER (expression1, expression2)
```

 POW and POWER are synonyms\. 

## Arguments<a name="r_POWER-arguments"></a>

 *expression1*   
Numeric expression to be raised\. Must be an integer, decimal, or floating\-point data type\. 

 *expression2*   
Power to raise *expression1*\. Must be an integer, decimal, or floating\-point data type\. 

## Return type<a name="r_POWER-return-type"></a>

POWER returns a DOUBLE PRECISION number\. 

## Examples<a name="r_POWER-examples"></a>

In the following example, the POWER function is used to forecast what ticket sales will look like in the next 10 years, based on the number of tickets sold in 2008 \(the result of the subquery\)\. The growth rate is set at 7% per year in this example\. 

```
select (select sum(qtysold) from sales, date
where sales.dateid=date.dateid
and year=2008) * pow((1+7::float/100),10) qty2010;

qty2010
------------------
679353.754088594
(1 row)
```

The following example is a variation on the previous example, with the growth rate at 7% per year but the interval set to months \(120 months over 10 years\): 

```
select (select sum(qtysold) from sales, date
where sales.dateid=date.dateid
and year=2008) * pow((1+7::float/100/12),120) qty2010;

qty2010
-----------------
694034.54678046
(1 row)
```