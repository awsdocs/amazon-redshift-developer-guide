# EXTRACT function<a name="r_EXTRACT_function"></a>

 The EXTRACT function returns a date or time part, such as a day, month, or year, hour, minute, second, millisecond, or microsecond from a TIMESTAMP value or expression, TIME, or TIMETZ\. 

## Syntax<a name="r_EXTRACT_function-synopsis"></a>

```
EXTRACT ( datepart FROM { TIMESTAMP 'literal' | timestamp | time | timetz } )
```

## Arguments<a name="r_EXTRACT_function-arguments"></a>

 *datepart*   
For possible values, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)\. 

 *literal*   
A timestamp value, enclosed in single quotation marks and preceded by the TIMESTAMP keyword\. 

 *timestamp* \| *times* \| *timestz*   
A TIMESTAMP, TIMESTAMPTZ, TIME, or TIMETZ column, or an expression that implicitly converts to a TIMESTAMP, TIMESTAMP WITH TIME ZONE, TIME, or TIMETZ\.

## Return type<a name="r_EXTRACT_function-return-type"></a>

INTEGER if the argument is TIMESTAMP, TIME, or TIMETZ

DOUBLE PRECISION if the argument is TIMESTAMPTZ

## Examples with a timestamp column<a name="r_EXTRACT_function-examples"></a>

The following example determines the week numbers for sales in which the price paid was $10,000 or more\. 

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

The following example returns the minute value from a literal timestamp value\. 

```
select extract(minute from timestamp '2009-09-09 12:08:43');
date_part
-----------
8
(1 row)
```

## Examples with a time column<a name="r_EXTRACT_function-examples-time"></a>

The following example table TIME\_TEST has a column TIME\_VAL \(type TIME\) with three values inserted\. 

```
select time_val from time_test;
            
time_val
---------------------
20:00:00
00:00:00.5550
00:58:00
```

The following example extracts the seconds from each time\_val\.

```
select extract(minute from time_val) as secs from time_test;
            
secs
-----------
         0
         0
         58
```

The following example extracts the hours from each timetz\_val\.

```
select extract(hour from time_val) as hours from time_test;
            
hours
-----------
         20
         0
         0
```

The following example extracts milliseconds from a literal value\.

```
select extract(ms from time '18:25:33.123456');
 date_part
-----------
     123
```

## Examples with a TIMETZ column<a name="r_EXTRACT_function-examples-timetz"></a>

The following example table TIMETZ\_TEST has a column TIMETZ\_VAL \(type TIMETZ\) with three values inserted\.

```
select timetz_val from timetz_test;
            
timetz_val
------------------
04:00:00+00
00:00:00.5550+00
05:58:00+00
```

The following example extracts the hours from each timetz\_val\.

```
select extract(hour from timetz_val) as hours from time_test;
            
hours
-----------
         4
         0
         5
```

The following example extracts milliseconds from a literal value\. Literals aren't converted to UTC before the extraction is processed\. 

```
select extract(hour from time '18:25:33.123456 EST');
 date_part
-----------
     18
```