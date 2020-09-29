# LAST\_DAY function<a name="r_LAST_DAY"></a>

LAST\_DAY returns the date of the last day of the month that contains *date*\. The return type is always DATE, regardless of the data type of the *date* argument\.

## Syntax<a name="r_LAST_DAY-synopsis"></a>

```
LAST_DAY ( { date | timestamp } )
```

## Arguments<a name="r_LAST_DAY-arguments"></a>

*date* \| *timestamp*

A date or timestamp column or an expression that implicitly converts to a date or timestamp\.

## Return type<a name="r_LAST_DAY-return-type"></a>

DATE

## Examples<a name="r_LAST_DAY-examples"></a>

The following example returns the date of the last day in the current month\.

```
select last_day(sysdate);

  last_day
------------
 2014-01-31
(1 row)
```

The following example returns the number of tickets sold for each of the last 7 days of the month\.

```
select datediff(day, saletime, last_day(saletime)) as "Days Remaining", sum(qtysold)
from sales
where datediff(day, saletime, last_day(saletime)) < 7
group by 1
order by 1;

 days remaining |  sum
----------------+-------
              0 | 10140
              1 | 11187
              2 | 11515
              3 | 11217
              4 | 11446
              5 | 11708
              6 | 10988
(7 rows)
```