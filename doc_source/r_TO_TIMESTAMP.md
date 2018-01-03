# TO\_TIMESTAMP Function<a name="r_TO_TIMESTAMP"></a>

TO\_TIMESTAMP converts a TIMESTAMP string to TIMESTAMPTZ\.

## Syntax<a name="r_TO_TIMESTAMP-syntax"></a>

```
to_timestamp ('timestamp', 'format')
```

## Arguments<a name="r_TO_TIMESTAMP-arguments"></a>

*timestamp*  
A string that represents a time stamp value in the format specified by *format*\. 

*format*  
The format for the *timestamp* value\. Formats that include a time zone \(**TZ**, **tz**, or **OF**\) are not supported as input\. For valid time stamp formats, see [Datetime Format Strings](r_FORMAT_strings.md)\.

## Return Type<a name="r_TO_TIMESTAMP-return-type"></a>

TIMESTAMPTZ