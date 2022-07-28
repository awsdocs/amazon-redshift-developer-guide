# TIMESTAMPTZ\_CMP\_TIMESTAMP function<a name="r_TIMESTAMPTZ_CMP_TIMESTAMP"></a>

TIMESTAMPTZ\_CMP\_TIMESTAMP compares the value of a timestamp with time zone expression with a timestamp expression\. If the timestamp with time zone and timestamp values are identical, the function returns 0\. If the timestamp with time zone is greater alphabetically, the function returns 1\. If the timestamp is greater, the function returns –1\. 

## Syntax<a name="r_TIMESTAMPTZ_CMP_TIMESTAMP-syntax"></a>

```
TIMESTAMPTZ_CMP_TIMESTAMP(timestamptz, timestamp)
```

## Arguments<a name="r_TIMESTAMPTZ_CMP_TIMESTAMP-arguments"></a>

 *timestamptz*   
A TIMESTAMPTZ column or an expression that implicitly converts to a timestamp with a time zone\.

 *timestamp*   
A TIMESTAMP column or an expression that implicitly converts to a timestamp\.

## Return type<a name="r_TIMESTAMPTZ_CMP_TIMESTAMP-return-type"></a>

INTEGER