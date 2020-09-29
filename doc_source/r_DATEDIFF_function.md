# DATEDIFF function<a name="r_DATEDIFF_function"></a>

DATEDIFF returns the difference between the date parts of two date or time expressions\. 

## Syntax<a name="r_DATEDIFF_function-synopsis"></a>

```
DATEDIFF ( datepart, {date|time|timetz|timestamp}, {date|time|time|timestamp} )
```

## Arguments<a name="r_DATEDIFF_function-arguments"></a>

 *datepart*   
The specific part of the date or time value \(year, month, or day, hour, minute, second, millisecond, or microsecond\) that the function operates on\. For more information, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)\.   
Specifically, DATEDIFF determines the number of date part boundaries that are crossed between two expressions\. For example, suppose that you're calculating the difference in years between two dates, `12-31-2008` and `01-01-2009`\. In this case, the function returns 1 year despite the fact that these dates are only one day apart\. If you are finding the difference in hours between two timestamps, `01-01-2009 8:30:00` and `01-01-2009 10:00:00`, the result is 2 hours\. If you are finding the difference in hours between two timestamps, `8:30:00` and `10:00:00`, the result is 2 hours\.

*date*\|*time*\|*timetz*\|*timestamp*  
A DATE, TIME, TIMETZ, or TIMESTAMP column or expressions that implicitly convert to a DATE, TIME, TIMETZ, or TIMESTAMP\. The expressions must both contain the specified date or time part\. If the second date or time is later than the first date or time, the result is positive\. If the second date or time is earlier than the first date or time, the result is negative\.

## Return type<a name="r_DATEDIFF_function-return-type"></a>

BIGINT

## Examples with a DATE column<a name="r_DATEDIFF_function-examples"></a>

The following example finds the difference, in number of weeks, between two literal date values\. 

```
select datediff(week,'2009-01-01','2009-12-31') as numweeks;

numweeks
----------
52
(1 row)
```

The following example finds the difference, in number of quarters, between a literal value in the past and today's date\. This example assumes that the current date is June 5, 2008\. You can name date parts in full or abbreviate them\. The default column name for the DATEDIFF function is DATE\_DIFF\. 

```
select datediff(qtr, '1998-07-01', current_date);

date_diff
-----------
40
(1 row)
```

The following example joins the SALES and LISTING tables to calculate how many days after they were listed any tickets were sold for listings 1000 through 1005\. The longest wait for sales of these listings was 15 days, and the shortest was less than one day \(0 days\)\. 

```
select priceperticket,
datediff(day, listtime, saletime) as wait
from sales, listing where sales.listid = listing.listid
and sales.listid between 1000 and 1005
order by wait desc, priceperticket desc;

priceperticket | wait
---------------+------
 96.00         |   15
 123.00        |   11
 131.00        |    9
 123.00        |    6
 129.00        |    4
 96.00         |    4
 96.00         |    0
(7 rows)
```

This example calculates the average number of hours sellers waited for all ticket sales\. 

```
select avg(datediff(hours, listtime, saletime)) as avgwait
from sales, listing
where sales.listid = listing.listid;

avgwait
---------
465
(1 row)
```

## Examples with a TIME column<a name="r_DATEDIFF_function-examples-time"></a>

The following example table TIME\_TEST has a column TIME\_VAL \(type TIME\) with three values inserted\.

```
select time_val from time_test;
            
time_val
---------------------
20:00:00
00:00:00.5550
00:58:00
```

The following example finds the difference in number of hours between the TIME\_VAL column and a time literal\.

```
select datediff(hour, time_val, time '15:24:45') from time_test;
         
 date_diff
-----------
        -5
        15
        15
```

The following example finds the difference in number of minutes between two literal time values\.

```
select datediff(minute, time '20:00:00', time '21:00:00') as nummins;  
         
nummins 
---------- 
60
```

## Examples with a TIMETZ column<a name="r_DATEDIFF_function-examples-timetz"></a>

The following example table TIMETZ\_TEST has a column TIMETZ\_VAL \(type TIMETZ\) with three values inserted\.

```
select timetz_val from timetz_test;
            
timetz_val
------------------
04:00:00+00
00:00:00.5550+00
05:58:00+00
```

The following example finds the differences in number of hours, between a TIMETZ literal and timetz\_val\. 

```
select datediff(hours, timetz '20:00:00 PST', timetz_val) as numhours from timetz_test;

numhours 
---------- 
0
-4
1
```

The following example finds the difference in number of hours, between two literal TIMETZ values\.

```
select datediff(hours, timetz '20:00:00 PST', timetz '00:58:00 EST') as numhours;
         
numhours 
---------- 
1
```