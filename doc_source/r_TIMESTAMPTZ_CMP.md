# TIMESTAMPTZ\_CMP function<a name="r_TIMESTAMPTZ_CMP"></a>

TIMESTAMPTZ\_CMP compares the value of two timestamp with time zone values and returns an integer\. If the timestamps are identical, the function returns 0\. If the first timestamp is greater alphabetically, the function returns 1\. If the second timestamp is greater, the function returns –1\.

## Syntax<a name="r_TIMESTAMPTZ_CMP-synopsis"></a>

```
TIMESTAMPTZ_CMP(timestamptz1, timestamptz2)
```

## Arguments<a name="r_TIMESTAMPTZ_CMP-arguments"></a>

 *timestamptz1*   
A TIMESTAMPTZ column or an expression that implicitly converts to a timestamp with time zone\.

 *timestamptz2*   
A TIMESTAMPTZ column or an expression that implicitly converts to a timestamp with time zone\.

## Return type<a name="r_TIMESTAMPTZ_CMP-return-type"></a>

INTEGER