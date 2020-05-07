# Datetime types<a name="r_Datetime_types"></a>

**Topics**
+ [Storage and ranges](#r_Datetime_types-storage-and-ranges)
+ [DATE](#r_Datetime_types-date)
+ [TIMESTAMP](#r_Datetime_types-timestamp)
+ [TIMESTAMPTZ](#r_Datetime_types-timestamptz)
+ [Examples with datetime types](r_Examples_with_datetime_types.md)
+ [Date and timestamp literals](r_Date_and_time_literals.md)
+ [Interval literals](r_interval_literals.md)

Datetime data types include DATE, TIMESTAMP, and TIMESTAMPTZ\.

## Storage and ranges<a name="r_Datetime_types-storage-and-ranges"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Datetime_types.html)

## DATE<a name="r_Datetime_types-date"></a>

Use the DATE data type to store simple calendar dates without time stamps\.

## TIMESTAMP<a name="r_Datetime_types-timestamp"></a>

TIMESTAMP is an alias of TIMESTAMP WITHOUT TIME ZONE\.

Use the TIMESTAMP data type to store complete time stamp values that include the date and the time of day\. 

TIMESTAMP columns store values with up to a maximum of 6 digits of precision for fractional seconds\.

If you insert a date into a TIMESTAMP column, or a date with a partial time stamp value, the value is implicitly converted into a full time stamp value with default values \(00\) for missing hours, minutes, and seconds\. Time zone values in input strings are ignored\.

By default, TIMESTAMP values are Coordinated Universal Time \(UTC\) in both user tables and Amazon Redshift system tables\. 

## TIMESTAMPTZ<a name="r_Datetime_types-timestamptz"></a>

TIMESTAMPTZ is an alias of TIMESTAMP WITH TIME ZONE\.

Use the TIMESTAMPTZ data type to input complete time stamp values that include the date, the time of day, and a time zone\. When an input value includes a time zone, Amazon Redshift uses the time zone to convert the value to Coordinated Universal Time \(UTC\) and stores the UTC value\.

To view a list of supported time zone names, execute the following command\. 

```
select pg_timezone_names();
```

 To view a list of supported time zone abbreviations, execute the following command\. 

```
select pg_timezone_abbrevs();
```

You can also find current information about time zones in the [IANA Time Zone Database](https://www.iana.org/time-zones)\.

The following table has examples of time zone formats\. 


| Format | Example | 
| --- | --- | 
|  day mon hh:mi:ss yyyy tz  |  17 Dec 07:37:16 1997 PST   | 
|  mm/dd/yyyy hh:mi:ss\.ss tz  |  12/17/1997 07:37:16\.00 PST  | 
|  mm/dd/yyyy hh:mi:ss\.ss tz  |  12/17/1997 07:37:16\.00 US/Pacific  | 
|  yyyy\-mm\-dd hh:mi:ss\+/\-tz  |  1997\-12\-17 07:37:16\-08   | 
| dd\.mm\.yyyy hh:mi:ss tz |  17\.12\.1997 07:37:16\.00 PST  | 

TIMESTAMPTZ columns store values with up to a maximum of 6 digits of precision for fractional seconds\.

If you insert a date into a TIMESTAMPTZ column, or a date with a partial time stamp, the value is implicitly converted into a full time stamp value with default values \(00\) for missing hours, minutes, and seconds\.

TIMESTAMPTZ values are UTC in user tables\.