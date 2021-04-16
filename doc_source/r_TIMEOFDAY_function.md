# TIMEOFDAY function<a name="r_TIMEOFDAY_function"></a>

TIMEOFDAY is a special alias used to return the weekday, date, and time as a string value\. It returns the time of day string for the current statement, even when it is within a transaction block\. 

## Syntax<a name="r_TIMEOFDAY_function-syntax"></a>

```
TIMEOFDAY()
```

## Return type<a name="r_TIMEOFDAY_function-return-type"></a>

VARCHAR

## Examples<a name="r_TIMEOFDAY_function-examples"></a>

The following example returns the current date and time by using the TIMEOFDAY function\. 

```
select timeofday();
timeofday
------------
Thu Sep 19 22:53:50.333525 2013 UTC
(1 row)
```