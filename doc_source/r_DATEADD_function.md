# DATEADD function<a name="r_DATEADD_function"></a>

Increments a date or time stamp value by a specified interval\. 

## Syntax<a name="r_DATEADD_function-synopsis"></a>

```
DATEADD( datepart, interval, {date|timestamp} )
```

This function returns a time stamp data type\. 

## Arguments<a name="r_DATEADD_function-arguments"></a>

 *datepart*   
The date part \(year, month, or day, for example\) that the function operates on\. See [Dateparts for Date or Time Stamp functions](r_Dateparts_for_datetime_functions.md)\. 

 *interval*   
An integer that specified the interval \(number of days, for example\) to add to the target expression\. A negative integer subtracts the interval\. 

 *date*\|*timestamp*  
A date or timestamp column or an expression that implicitly converts to a date or time stamp\. The date or time stamp expression must contain the specified date part\. 

## Return type<a name="r_DATEADD_function-return-type"></a>

TIMESTAMP

## Examples<a name="r_DATEADD_function-examples"></a>

Add 30 days to each date in November that exists in the DATE table: 

```
select dateadd(day,30,caldate) as novplus30
from date
where month='NOV'
order by dateid;

novplus30
---------------------
2008-12-01 00:00:00
2008-12-02 00:00:00
2008-12-03 00:00:00
...
(30 rows)
```

 Add 18 months to a literal date value: 

```
select dateadd(month,18,'2008-02-28');

date_add
---------------------
2009-08-28 00:00:00
(1 row)
```

The default column name for a DATEADD function is DATE\_ADD\. The default time stamp for a date value is `00:00:00`\. 

Add 30 minutes to a date value that does not specify a time stamp: 

```
select dateadd(m,30,'2008-02-28');

date_add
---------------------
2008-02-28 00:30:00
(1 row)
```

You can name dateparts in full or abbreviate them; in this case, *m* stands for minutes, not months\. 

## Usage notes<a name="r_DATEADD_usage_notes"></a>

 The DATEADD\(month, \.\.\.\) and ADD\_MONTHS functions handle dates that fall at the ends of months differently\. 
+ ADD\_MONTHS: If the date you are adding to is the last day of the month, the result is always the last day of the result month, regardless of the length of the month\. For example, April 30th \+ 1 month is May 31st: 

  ```
  select add_months('2008-04-30',1);
  
  add_months
  ---------------------
  2008-05-31 00:00:00
  (1 row)
  ```
+ DATEADD: If there are fewer days in the date you are adding to than in the result month, the result will be the corresponding day of the result month, not the last day of that month\. For example, April 30th \+ 1 month is May 30th: 

  ```
  select dateadd(month,1,'2008-04-30');
  
  date_add
  ---------------------
  2008-05-30 00:00:00
  (1 row)
  ```

The DATEADD function handles the leap year date 02\-29 differently when using dateadd\(month, 12,…\) or dateadd\(year, 1, …\)\. 

```
select dateadd(month,12,'2016-02-29');
date_add
---------------------
2017-02-28 00:00:00

select dateadd(year, 1, '2016-02-29');
date_add       
---------------------
2017-03-01 00:00:00
```