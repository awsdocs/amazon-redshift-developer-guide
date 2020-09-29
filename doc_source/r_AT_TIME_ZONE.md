# AT TIME ZONE function<a name="r_AT_TIME_ZONE"></a>

AT TIME ZONE specifies which time zone to use with a TIMESTAMP or TIMESTAMPTZ expression\.

## Syntax<a name="r_AT_TIME_ZONE-syntax"></a>

```
AT TIME ZONE 'timezone'
```

## Arguments<a name="r_AT_TIME_ZONE-arguments"></a>

*timezone*  
The time zone for the return value\. The time zone can be specified as a time zone name \(such as **'Africa/Kampala'** or **'Singapore'**\) or as a time zone abbreviation \(such as **'UTC'** or **'PDT'**\)\.   
To view a list of supported time zone names, execute the following command\.   

```
select pg_timezone_names();
```
 To view a list of supported time zone abbreviations, execute the following command\.   

```
select pg_timezone_abbrevs();
```
 For more information and examples, see [Time zone usage notes](CONVERT_TIMEZONE.md#CONVERT_TIMEZONE-usage-notes)\.

## Return type<a name="r_AT_TIME_ZONE-return-type"></a>

TIMESTAMPTZ when used with a TIMESTAMP expression\. TIMESTAMP when used with a TIMESTAMPTZ expression\. 

## Examples<a name="r_AT_TIME_ZONE-examples"></a>

The following example converts a timestamp value without time zone and interprets it as MST time \(UTC–7\), which is then converted to PST \(UTC–8\) for display\.

```
SELECT TIMESTAMP '2001-02-16 20:38:40' AT TIME ZONE 'MST';

  timestamptz 
------------------------
'2001-02-16 19:38:40-08'
```

The following example takes an input timestamp with a time zone value where the specified time zone is UTC\-5 \(EST\) and converts it to MST \(UTC\-7\)\. 

```
SELECT TIMESTAMP WITH TIME ZONE '2001-02-16 20:38:40-05' AT TIME ZONE 'MST';

  timestamp
------------------------
'2001-02-16 18:38:40'
```