# TIMESTAMP\_CMP\_TIMESTAMPTZ function<a name="r_TIMESTAMP_CMP_TIMESTAMPTZ"></a>

TIMESTAMP\_CMP\_TIMESTAMPTZ compares the value of a time stamp expression with a time stamp with time zone expression\. If the time stamp and time stamp with time zone values are identical, the function returns 0\. If the time stamp is greater alphabetically, the function returns 1\. If the time stamp with time zone is greater, the function returns –1\.

## Syntax<a name="r_TIMESTAMP_CMP_TIMESTAMPTZ-syntax"></a>

```
TIMESTAMP_CMP_TIMESTAMPTZ(timestamp, timestamptz)
```

## Arguments<a name="r_TIMESTAMP_CMP_TIMESTAMPTZ-arguments"></a>

 *timestamp*   
A TIMESTAMP column or an expression that implicitly converts to a time stamp\.

 *timestamptz*   
A TIMESTAMP column or an expression that implicitly converts to a time stamp with a time zone\.

## Return type<a name="r_TIMESTAMP_CMP_TIMESTAMPTZ-return-type"></a>

INTEGER