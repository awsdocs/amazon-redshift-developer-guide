# DATE\_PART function<a name="r_DATE_PART_function"></a>

DATE\_PART extracts date part values from an expression\. DATE\_PART is a synonym of the PGDATE\_PART function\. 

## Syntax<a name="r_DATE_PART_function-synopsis"></a>

```
DATE_PART ( datepart, {date|timestamp} )
```

## Arguments<a name="r_DATE_PART_function-arguments"></a>

 *datepart*   
The specific part of the date value \(year, month, or day, for example\) that the function operates on\. For more information, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)\. 

\{*date*\|*timestamp*\}  
A date or timestamp column or an expression that implicitly converts to a date or timestamp\. The expression must be a date or timestamp expression that contains the specified date part\. 

## Return type<a name="r_DATE_PART_function-return-type"></a>

DOUBLE

## Examples<a name="r_DATE_PART_function-examples"></a>

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

The following example applies the DATE\_PART function to a literal date value\.

```
select date_part(minute, '2009-01-01 02:08:01');

pgdate_part
-------------
8
(1 row)
```

The default column name for the DATE\_PART function is PGDATE\_PART\. 