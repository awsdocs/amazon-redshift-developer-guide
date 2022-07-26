# DATE\_PART function<a name="r_DATE_PART_function"></a>

DATE\_PART extracts date part values from an expression\. DATE\_PART is a synonym of the PGDATE\_PART function\. 

## Syntax<a name="r_DATE_PART_function-synopsis"></a>

```
DATE_PART(datepart, {date|timestamp})
```

## Arguments<a name="r_DATE_PART_function-arguments"></a>

 *datepart*   
An identifier literal or string of the specific part of the date value \(year, month, or day, for example\) that the function operates on\. For more information, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)\. 

\{*date*\|*timestamp*\}  
A date or timestamp column or an expression that implicitly converts to a date or timestamp\. The expression must be a date or timestamp expression that contains the specified date part\. 

## Return type<a name="r_DATE_PART_function-return-type"></a>

DOUBLE

## Examples<a name="r_DATE_PART_function-examples"></a>

The default column name for the DATE\_PART function is `pgdate_part`\. For more information about the data used in some of these examples, see [Sample database](c_sampledb.md)\.

The following example finds the minute from a timestamp literal\. 

```
SELECT DATE_PART(minute, timestamp '20230104 04:05:06.789');
pgdate_part
6
```

The following example finds the week number from a timestamp literal\. The week number calculation follows the ISO 8601 standard\. For more information, see [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) in Wikipedia\.

```
SELECT DATE_PART(week, timestamp '20220502 04:05:06.789');
pgdate_part
18
```

The following example finds the day of the month from a timestamp literal\.

```
SELECT DATE_PART(day, timestamp '20220502 04:05:06.789');
pgdate_part
2
```

The following example finds the day of the week from a timestamp literal\. The week number calculation follows the ISO 8601 standard\. For more information, see [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) in Wikipedia\.

```
SELECT DATE_PART(dayofweek, timestamp '20220502 04:05:06.789');
pgdate_part
1
```

The following example finds the century from a timestamp literal\. The century calculation follows the ISO 8601 standard\. For more information, see [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) in Wikipedia\.

```
SELECT DATE_PART(century, timestamp '20220502 04:05:06.789');
pgdate_part
21
```

The following example finds the month from a date literal\. 

```
SELECT DATE_PART(month, date '20220502');
pgdate_part
5
```

The following example applies the DATE\_PART function to a column in a table\. 

```
select date_part(w, listtime) as weeks, listtime
from listing where listid=10;

weeks |      listtime
------+---------------------
 25   | 2008-06-17 09:44:54
(1 row)
```

You can name date parts in full or abbreviate them; in this case, *w* stands for weeks\. 

The day of week date part returns an integer from 0\-6, starting with Sunday\. Use DATE\_PART with dow \(DAYOFWEEK\) to view events on a Saturday\. 

```
select date_part(dow, starttime) as dow,
starttime from event
where date_part(dow, starttime)=6
order by 2,1;

 dow |      starttime
-----+---------------------
   6 | 2008-01-05 14:00:00
   6 | 2008-01-05 14:00:00
   6 | 2008-01-05 14:00:00
   6 | 2008-01-05 14:00:00
...
(1147 rows)
```