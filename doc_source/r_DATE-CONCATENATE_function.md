# \+ \(Concatenation\) operator<a name="r_DATE-CONCATENATE_function"></a>

Concatenates a DATE to a TIME or TIMETZ on either side of the \+ symbol and returns a TIMESTAMP or TIMESTAMPTZ\. 

## Syntax<a name="r_DATE-CONCATENATE_function-synopsis"></a>

```
date + time
```

```
date + timetz
```

The order of the arguments can be reversed\.

## Arguments<a name="r_DATE-CONCATENATE_function-arguments"></a>

 *date*   
A DATE column or an expression that implicitly converts to a DATE\. 

 *time*   
A TIME column or an expression that implicitly converts to a TIME\. 

 *timetz*   
A TIMETZ column or an expression that implicitly converts to a TIMETZ\. 

## Return type<a name="r_DATE-CONCATENATE_function-return-type"></a>

TIMESTAMP if input is *time*\. 

TIMESTAMPTZ if input is *timetz*\. 

## Examples with a time column<a name="r_DATE-CONCATENATE_function-examples-time"></a>

The following example table TIME\_TEST has a column TIME\_VAL \(type TIME\) with three values inserted\. 

```
select time_val from time_test;
            
time_val
---------------------
20:00:00
00:00:00.5550
00:58:00
```

The following example concatenates a date literal and a TIME\_VAL column\.

```
select date '2000-01-02' + time_val as ts from time_test;
            
ts
---------------------
2000-01-02 20:00:00
2000-01-02 00:00:00.5550
2000-01-02 00:58:00
```

The following example concatenates a date literal and a time literal\. 

```
select date '2000-01-01' + time '20:00:00' as ts;
            
         ts
---------------------
 2000-01-01 20:00:00
```

## Examples with a TIMETZ column<a name="r_DATE-CONCATENATE_function-examples-timetz"></a>

The following example table TIMETZ\_TEST has a column TIMETZ\_VAL \(type TIMETZ\) with three values inserted\. 

```
select timetz_val from timetz_test;
            
timetz_val
------------------
04:00:00+00
00:00:00.5550+00
05:58:00+00
```

The following example concatenates a date literal and a TIMETZ\_VAL column\. 

```
select date '2000-01-01' + timetz_val as ts from timetz_test;
ts
---------------------
2000-01-01 04:00:00+00
2000-01-01 00:00:00.5550+00
2000-01-01 05:58:00+00
```

The following example concatenates a DATE literal and a TIMETZ literal\.

```
select date '2000-01-01' + timetz '20:00:00 PST' as ts;
            
           ts
------------------------
 2000-01-02 04:00:00+00
```