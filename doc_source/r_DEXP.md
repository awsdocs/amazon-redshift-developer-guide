# DEXP function<a name="r_DEXP"></a>

The DEXP function returns the exponential value in scientific notation for a double precision number\. The only difference between the DEXP and EXP functions is that the parameter for DEXP must be a double precision\. 

## Syntax<a name="r_DEXP-synopsis"></a>

```
DEXP(number)
```

## Argument<a name="r_DEXP-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_DEXP-return-type"></a>

The DEXP function returns a double precision number\. 

## Example<a name="r_DEXP-example"></a>

Use the DEXP function to forecast ticket sales based on a continuous growth pattern\. In this example, the subquery returns the number of tickets sold in 2008\. That result is multiplied by the result of the DEXP function, which specifies a continuous growth rate of 7% over 10 years\. 

```
select (select sum(qtysold) from sales, date
where sales.dateid=date.dateid
and year=2008) * dexp((7::float/100)*10) qty2010;

qty2010
------------------
695447.483772222
(1 row)
```