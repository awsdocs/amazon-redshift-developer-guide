# DATE\_CMP\_TIMESTAMPTZ function<a name="r_DATE_CMP_TIMESTAMPTZ"></a>

DATE\_CMP\_TIMESTAMPTZ compares a date to a time stamp with time zone\. If the date and time stamp values are identical, the function returns 0\. If the date is greater alphabetically, the function returns 1\. If the time stamp is greater, the function returns –1\.

## Syntax<a name="r_DATE_CMP_TIMESTAMPTZ-syntax"></a>

```
DATE_CMP_TIMESTAMPTZ(date, timestamptz)
```

## Arguments<a name="r_DATE_CMP_TIMESTAMPTZ-arguments"></a>

 *date*   
A DATE column or an expression that implicitly converts to a date\.

 *timestamptz*   
A TIMESTAMPTZ column or an expression that implicitly converts to a time stamp with a time zone\.

## Return type<a name="r_DATE_CMP_TIMESTAMPTZ-return-type"></a>

INTEGER