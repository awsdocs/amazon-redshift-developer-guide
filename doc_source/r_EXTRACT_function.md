# EXTRACT function<a name="r_EXTRACT_function"></a>

 The EXTRACT function returns a date part, such as a day, month, or year, from a time stamp value or expression\. 

## Syntax<a name="r_EXTRACT_function-synopsis"></a>

```
EXTRACT ( datepart FROM { TIMESTAMP 'literal' | timestamp } )
```

## Arguments<a name="r_EXTRACT_function-arguments"></a>

 *datepart*   
For possible values, see [Dateparts for Date or Time Stamp functions](r_Dateparts_for_datetime_functions.md)\. 

 *literal*   
A time stamp value, enclosed in single quotation marks and preceded by the TIMESTAMP keyword\. 

 *timestamp*   
A TIMESTAMP or TIMESTAMPTZ column, or an expression that implicitly converts to a time stamp or time stamp with time zone\.

## Return type<a name="r_EXTRACT_function-return-type"></a>

INTEGER if the argument is TIMESTAMP

DOUBLE PRECISION if the argument is TIMESTAMPTZ

## Examples<a name="r_EXTRACT_function-examples"></a>

Determine the week numbers for sales in which the price paid was $10,000 or more\. 

```
select salesid, extract(week from saletime) as weeknum
from sales where pricepaid > 9999 order by 2;

salesid | weeknum
--------+---------
 159073 |       6
 160318 |       8
 161723 |      26
(3 rows)
```

Return the minute value from a literal time stamp value\. 

```
select extract(minute from timestamp '2009-09-09 12:08:43');
date_part
-----------
8
(1 row)
```