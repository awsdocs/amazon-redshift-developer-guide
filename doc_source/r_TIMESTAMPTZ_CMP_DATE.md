# TIMESTAMPTZ\_CMP\_DATE function<a name="r_TIMESTAMPTZ_CMP_DATE"></a>

TIMESTAMPTZ\_CMP\_DATE compares the value of a timestamp and a date\. If the timestamp and date values are identical, the function returns 0\. If the timestamp is greater alphabetically, the function returns 1\. If the date is greater, the function returns –1\.

## Syntax<a name="r_TIMESTAMPTZ_CMP_DATE-syntax"></a>

```
TIMESTAMPTZ_CMP_DATE(timestamptz, date)
```

## Arguments<a name="r_TIMESTAMPTZ_CMP_DATE-arguments"></a>

 *timestamptz*   
A TIMESTAMPTZ column or an expression that implicitly converts to a timestamp with a time zone\.

 *date*   
A date column or an expression that implicitly converts to a date\.

## Return type<a name="r_TIMESTAMPTZ_CMP_DATE-return-type"></a>

INTEGER