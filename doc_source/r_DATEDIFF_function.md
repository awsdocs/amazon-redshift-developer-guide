# DATEDIFF function<a name="r_DATEDIFF_function"></a>

DATEDIFF returns the difference between the date parts of two date or time expressions\. 

## Syntax<a name="r_DATEDIFF_function-synopsis"></a>

```
DATEDIFF ( datepart, {date|timestamp}, {date|timestamp} )
```

## Arguments<a name="r_DATEDIFF_function-arguments"></a>

 *datepart*   
The specific part of the date value \(year, month, or day, for example\) that the function operates on\. For more information, see [Dateparts for Date or Time Stamp functions](r_Dateparts_for_datetime_functions.md)\.   
Specifically, DATEDIFF determines the number of *datepart boundaries* that are crossed between two expressions\. For example, if you are calculating the difference in years between two dates, `12-31-2008` and `01-01-2009`, the function returns 1 year despite the fact that these dates are only one day apart\. If you are finding the difference in hours between two time stamps, `01-01-2009 8:30:00` and `01-01-2009 10:00:00`, the result is 2 hours\. 

*date*\|*timestamp*  
A date or timestamp columns or expressions that implicitly convert to a date or time stamp\. The expressions must both contain the specified date part\. If the second date or time is later than the first date or time, the result is positive\. If the second date or time is earlier than the first date or time, the result is negative\.

## Return type<a name="r_DATEDIFF_function-return-type"></a>

BIGINT

## Examples<a name="r_DATEDIFF_function-examples"></a>

Find the difference, in number of weeks, between two literal date values: 

```
select datediff(week,'2009-01-01','2009-12-31') as numweeks;

numweeks
----------
52
(1 row)
```

Find the difference, in number of quarters, between a literal value in the past and today's date\. This example assumes that the current date is June 5, 2008\. You can name dateparts in full or abbreviate them\. The default column name for the DATEDIFF function is DATE\_DIFF\. 

```
select datediff(qtr, '1998-07-01', current_date);

date_diff
-----------
40
(1 row)
```

This example joins the SALES and LISTING tables to calculate how many days after they were listed any tickets were sold for listings 1000 through 1005\. The longest wait for sales of these listings was 15 days, and the shortest was less than one day \(0 days\)\. 

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