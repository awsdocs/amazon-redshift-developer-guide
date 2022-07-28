# TRUNC function<a name="r_TRUNC"></a>

The TRUNC function truncates numbers to the previous integer or decimal\. 

The TRUNC function can optionally include a second argument as an integer to indicate the number of decimal places for rounding, in either direction\. When you don't provide the second argument, the function rounds to the nearest whole number\. When the second argument *>n*is specified, the function rounds to the nearest number with *>n* decimal places of precision\. This function also truncates a timestamp and returns a date\.

## Syntax<a name="r_TRUNC-synopsis"></a>

```
TRUNC (number [ , integer ] |
timestamp )
```

## Arguments<a name="r_TRUNC-arguments"></a>

 *number*   
A number or expression that evaluates to a number\. It can be the DECIMAL, FLOAT8 or SUPER type\. Amazon Redshift can convert other data types per the implicit conversion rules\. 

 *integer* \(optional\)   
An integer that indicates the number of decimal places of precision, in either direction\. If no integer is provided, the number is truncated as a whole number; if an integer is specified, the number is truncated to the specified decimal place\. This isn't supported for the SUPER data type\.

 *timestamp*   
The function can also return the date from a timestamp\. \(To return a timestamp value with `00:00:00` as the time, cast the function result to a timestamp\.\) 

## Return type<a name="r_TRUNC-return-type"></a>

TRUNC returns the same data type as the first input argument\. For timestamps, TRUNC returns a date\. 

When the input is of the SUPER type, the output retains the same dynamic type as the input while the static type remains the SUPER type\. When the dynamic type of SUPER isn't a number, Amazon Redshift returns a null\.

## Examples<a name="r_TRUNC-examples"></a>

Truncate the commission paid for a given sales transaction\. 

```
select commission, trunc(commission)
from sales where salesid=784;

commission | trunc
-----------+-------
    111.15 |   111

(1 row)
```

Truncate the same commission value to the first decimal place\. 

```
select commission, trunc(commission,1)
from sales where salesid=784;

commission | trunc
-----------+-------
    111.15 | 111.1

(1 row)
```

Truncate the commission with a negative value for the second argument; `111.15` is rounded down to `110`\. 

```
select commission, trunc(commission,-1)
from sales where salesid=784;

commission | trunc
-----------+-------
    111.15 |   110
(1 row)
```

Return the date portion from the result of the SYSDATE function \(which returns a timestamp\): 

```
select sysdate;

timestamp
----------------------------
2011-07-21 10:32:38.248109
(1 row)

select trunc(sysdate);

trunc
------------
2011-07-21
(1 row)
```

Apply the TRUNC function to a TIMESTAMP column\. The return type is a date\. 

```
select trunc(starttime) from event
order by eventid limit 1;

trunc
------------
2008-01-25
(1 row)
```