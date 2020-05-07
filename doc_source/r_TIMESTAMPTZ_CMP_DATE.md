# TIMESTAMPTZ\_CMP\_DATE function<a name="r_TIMESTAMPTZ_CMP_DATE"></a>

TIMESTAMPTZ\_CMP\_DATE compares the value of a time stamp and a date\. If the time stamp and date values are identical, the function returns 0\. If the time stamp is greater alphabetically, the function returns 1\. If the date is greater, the function returns –1\.

## Syntax<a name="r_TIMESTAMPTZ_CMP_DATE-syntax"></a>

```
TIMESTAMPTZ_CMP_DATE(timestamptz, date)
```

## Arguments<a name="r_TIMESTAMPTZ_CMP_DATE-arguments"></a>

 *timestamptz*   
A TIMESTAMPTZ column or an expression that implicitly converts to a time stamp with a time zone\.

 *date*   
A date column or an expression that implicitly converts to a date\.

## Return type<a name="r_TIMESTAMPTZ_CMP_DATE-return-type"></a>

INTEGER