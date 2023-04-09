# EXTRACT function<a name="r_EXTRACT_function"></a>

The EXTRACT function returns a date or time part from a TIMESTAMP, TIMESTAMPTZ, TIME, or TIMETZ value\. Examples include a day, month, year, hour, minute, second, millisecond, or microsecond from a timestamp\.

## Syntax<a name="r_EXTRACT_function-synopsis"></a>

```
EXTRACT(datepart FROM source)
```

## Arguments<a name="r_EXTRACT_function-arguments"></a>

 *datepart*   
The subfield of a date or time to extract, such as a day, month, year, hour, minute, second, millisecond, or microsecond\. For possible values, see [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)\. 

 *source*   
A column or expression that evaluates to a data type of TIMESTAMP, TIMESTAMPTZ, TIME, or TIMETZ\. 

## Return type<a name="r_EXTRACT_function-return-type"></a>

INTEGER if the *source* value evaluates to data type TIMESTAMP, TIME, or TIMETZ\.

DOUBLE PRECISION if the *source* value evaluates to data type TIMESTAMPTZ\.

## Examples with TIMESTAMP<a name="r_EXTRACT_function-examples"></a>

The following example determines the week numbers for sales in which the price paid was $10,000 or more\. This example uses the TICKIT data\. For more information, see [Sample database](c_sampledb.md)

```
select salesid, extract(week from saletime) as weeknum
from sales 
where pricepaid > 9999 
order by 2;

salesid | weeknum
--------+---------
 159073 |       6
 160318 |       8
 161723 |      26
```

The following example returns the minute value from a literal timestamp value\. 

```
select extract(minute from timestamp '2009-09-09 12:08:43');
            
date_part
-----------
8
```

The following example returns the millisecond value from a literal timestamp value\. 

```
select extract(ms from timestamp '2009-09-09 12:08:43.101');
            
date_part
-----------
101
```

## Examples with TIMESTAMPTZ<a name="r_EXTRACT_function-examples-timestamptz"></a>

The following example returns the year value from a literal timestamptz value\. 

```
select extract(year from timestamptz '1.12.1997 07:37:16.00 PST');
            
date_part
-----------
1997
```

## Examples with TIME<a name="r_EXTRACT_function-examples-time"></a>

The following example table TIME\_TEST has a column TIME\_VAL \(type TIME\) with three values inserted\. 

```
select time_val from time_test;
            
time_val
---------------------
20:00:00
00:00:00.5550
00:58:00
```

The following example extracts the minutes from each time\_val\.

```
select extract(minute from time_val) as minutes from time_test;
            
minutes
-----------
         0
         0
         58
```

The following example extracts the hours from each time\_val\.

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

## Examples with TIMETZ<a name="r_EXTRACT_function-examples-timetz"></a>

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
select extract(ms from timetz '18:25:33.123456 EST');
            
 date_part
-----------
     123
```

The following example returns the timezone offset hour from UTC from a literal timetz value\. 

```
select extract(timezone_hour from timetz '1.12.1997 07:37:16.00 PDT');
            
date_part
-----------
-7
```