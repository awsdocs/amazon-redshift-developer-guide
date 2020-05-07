# Mathematical operator symbols<a name="r_OPERATOR_SYMBOLS"></a>

 The following table lists the supported mathematical operators\. 

## Supported operators<a name="r_OPERATOR_SYMBOLS-supported-operators"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_OPERATOR_SYMBOLS.html)

## Examples<a name="r_OPERATOR_SYMBOLS-examples"></a>

Calculate the commission paid plus a $2\.00 handling for a given transaction: 

```
select commission, (commission + 2.00) as comm
from sales where salesid=10000;

commission | comm
------------+-------
28.05 | 30.05
(1 row)
```

Calculate 20 percent of the sales price for a given transaction: 

```
select pricepaid, (pricepaid * .20) as twentypct
from sales where salesid=10000;

pricepaid | twentypct
-----------+-----------
187.00 |    37.400
(1 row)
```

Forecast ticket sales based on a continuous growth pattern\. In this example, the subquery returns the number of tickets sold in 2008\. That result is multiplied exponentially by a continuous growth rate of 5% over 10 years\. 

```
select (select sum(qtysold) from sales, date
where sales.dateid=date.dateid and year=2008)
^ ((5::float/100)*10) as qty10years;

qty10years
------------------
587.664019657491
(1 row)
```

Find the total price paid and commission for sales with a date ID that is greater than or equal to 2000\. Then subtract the total commission from the total price paid\. 

```
select sum (pricepaid) as sum_price, dateid,
sum (commission) as sum_comm, (sum (pricepaid) - sum (commission)) as value
from sales where dateid >= 2000
group by dateid order by dateid limit 10;

 sum_price | dateid | sum_comm |   value
-----------+--------+----------+-----------
 364445.00 |   2044 | 54666.75 | 309778.25
 349344.00 |   2112 | 52401.60 | 296942.40
 343756.00 |   2124 | 51563.40 | 292192.60
 378595.00 |   2116 | 56789.25 | 321805.75
 328725.00 |   2080 | 49308.75 | 279416.25
 349554.00 |   2028 | 52433.10 | 297120.90
 249207.00 |   2164 | 37381.05 | 211825.95
 285202.00 |   2064 | 42780.30 | 242421.70
 320945.00 |   2012 | 48141.75 | 272803.25
 321096.00 |   2016 | 48164.40 | 272931.60
(10 rows)
```