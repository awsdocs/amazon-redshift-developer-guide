# MONTHS\_BETWEEN function<a name="r_MONTHS_BETWEEN_function"></a>

MONTHS\_BETWEEN determines the number of months between two dates\.

If the first date is later than the second date, the result is positive; otherwise, the result is negative\.

If either argument is null, the result is NULL\.

## Syntax<a name="r_MONTHS_BETWEEN_function-synopsis"></a>

```
MONTHS_BETWEEN ( date1, date2 )
```

## Arguments<a name="r_MONTHS_BETWEEN_function-arguments"></a>

 *date1*   
An expression, such as a column name, that evaluates to a valid date or timestamp value\.

 *date2*   
An expression, such as a column name, that evaluates to a valid date or timestamp value\.

## Return type<a name="r_MONTHS_BETWEEN_function-return-type"></a>

FLOAT8

The whole number portion of the result is based on the difference between the year and month values of the dates\. The fractional portion of the result is calculated from the day and timestamp values of the dates and assumes a 31\-day month\.

If *date1* and *date2* both contain the same date within a month \(for example, 1/15/14 and 2/15/14\) or the last day of the month \(for example, 8/31/14 and 9/30/14\), then the result is a whole number based on the year and month values of the dates, regardless of whether the timestamp portion matches, if present\.

## Examples<a name="r_MONTHS_BETWEEN_function-examples"></a>

The following example returns the months between 1/18/1969 and 3/18/1969\. 

```
select months_between('1969-01-18', '1969-03-18')
as months; 

months
----------
-2
```

 The following example returns the months between the first and last showings of an event\. 

```
select eventname, 
min(starttime) as first_show,
max(starttime) as last_show,
months_between(max(starttime),min(starttime)) as month_diff
from event 
group by eventname
order by eventname
limit 5;

eventname         first_show             last_show              month_diff
---------------------------------------------------------------------------
.38 Special       2008-01-21 19:30:00.0  2008-12-25 15:00:00.0  11.12
3 Doors Down      2008-01-03 15:00:00.0  2008-12-01 19:30:00.0  10.94
70s Soul Jam      2008-01-16 19:30:00.0  2008-12-07 14:00:00.0  10.7
A Bronx Tale      2008-01-21 19:00:00.0  2008-12-15 15:00:00.0  10.8
A Catered Affair  2008-01-08 19:30:00.0  2008-12-19 19:00:00.0  11.35
```