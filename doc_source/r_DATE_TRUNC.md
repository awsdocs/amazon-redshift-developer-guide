# DATE\_TRUNC function<a name="r_DATE_TRUNC"></a>

The DATE\_TRUNC function truncates a timestamp expression or literal based on the date part that you specify, such as hour, day, or month\. 

## Syntax<a name="r_DATE_TRUNC-synopsis"></a>

```
DATE_TRUNC('datepart', timestamp)
```

## Arguments<a name="r_DATE_TRUNC-arguments"></a>

 *datepart*   
The date part to which to truncate the timestamp value\. The input *timestamp* is truncated to the precision of the input *datepart*\. For example, `month` truncates to the first day of the month\. Valid formats are as follows:   
+ microsecond, microseconds
+ millisecond, milliseconds
+ second, seconds
+ minute, minutes
+ hour, hours
+ day, days
+ week, weeks
+ month, months
+ quarter, quarters
+ year, years
+ decade, decades
+ century, centuries
+ millennium, millennia
For more information about abbreviations of some formats, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)

 *timestamp*   
A timestamp column or an expression that implicitly converts to a timestamp\.

## Return type<a name="r_DATE_TRUNC-return-type"></a>

TIMESTAMP

## Examples<a name="r_DATE_TRUNC-example"></a>

Truncate the input timestamp to the second\.

```
SELECT DATE_TRUNC('second', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-30 04:05:06
```

Truncate the input timestamp to the minute\.

```
SELECT DATE_TRUNC('minute', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-30 04:05:00
```

Truncate the input timestamp to the hour\.

```
SELECT DATE_TRUNC('hour', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-30 04:00:00
```

Truncate the input timestamp to the day\.

```
SELECT DATE_TRUNC('day', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-30 00:00:00
```

Truncate the input timestamp to the first day of a month\.

```
SELECT DATE_TRUNC('month', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-01 00:00:00
```

Truncate the input timestamp to the first day of a quarter\.

```
SELECT DATE_TRUNC('quarter', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-04-01 00:00:00
```

Truncate the input timestamp to the first day of a year\.

```
SELECT DATE_TRUNC('year', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2020-01-01 00:00:00
```

Truncate the input timestamp to the first day of a century\.

```
SELECT DATE_TRUNC('millennium', TIMESTAMP '20200430 04:05:06.789');
date_trunc
2001-01-01 00:00:00
```

Truncate the input timestamp to the Monday of a week\.

```
select date_trunc('week', TIMESTAMP '20220430 04:05:06.789');
date_trunc
2022-04-25 00:00:00
```

In the following example, the DATE\_TRUNC function uses the 'week' date part to return the date for the Monday of each week\. 

```
select date_trunc('week', saletime), sum(pricepaid) from sales where
saletime like '2008-09%' group by date_trunc('week', saletime) order by 1;

date_trunc  |    sum
------------+-------------
2008-09-01  | 2474899.00
2008-09-08  | 2412354.00
2008-09-15  | 2364707.00
2008-09-22  | 2359351.00
2008-09-29  |  705249.00
(5 rows)
```