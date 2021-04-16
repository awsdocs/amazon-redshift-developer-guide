# TO\_TIMESTAMP function<a name="r_TO_TIMESTAMP"></a>

TO\_TIMESTAMP converts a TIMESTAMP string to TIMESTAMPTZ\.

## Syntax<a name="r_TO_TIMESTAMP-syntax"></a>

```
to_timestamp ('timestamp', 'format')
```

## Arguments<a name="r_TO_TIMESTAMP-arguments"></a>

*timestamp*  
A string that represents a timestamp value in the format specified by *format*\. 

*format*  
The format for the *timestamp* value\. Formats that include a time zone \(**TZ**, **tz**, or **OF**\) are not supported as input\. For valid timestamp formats, see [Datetime format strings](r_FORMAT_strings.md)\.

## Return type<a name="r_TO_TIMESTAMP-return-type"></a>

TIMESTAMPTZ

## Examples<a name="r_TO_TIMESTAMP-examples"></a>

The following example demonstrates using the TO\_TIMESTAMP function to convert a TIMESTAMP string to a TIMESTAMPTZ\. 

```
select sysdate,
to_timestamp (sysdate, 'HH24:MI:SS') as seconds;

timestamp          |seconds
-------------------|----------------------
2018-05-17 23:54:51|0001-03-24 18:05:17.0
```