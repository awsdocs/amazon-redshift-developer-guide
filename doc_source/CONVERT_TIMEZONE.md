# CONVERT\_TIMEZONE function<a name="CONVERT_TIMEZONE"></a>

CONVERT\_TIMEZONE converts a timestamp from one time zone to another\. 

## Syntax<a name="CONVERT_TIMEZONE-syntax"></a>

```
CONVERT_TIMEZONE ( ['source_timezone',] 'target_timezone', 'timestamp')
```

## Arguments<a name="CONVERT_TIMEZONE-arguments"></a>

*source\_timezone*  
\(Optional\) The time zone of the current timestamp\. The default is UTC\. For more information, see [Time zone usage notes](#CONVERT_TIMEZONE-usage-notes)\.

*target\_timezone*   
The time zone for the new timestamp\. For more information, see [Time zone usage notes](#CONVERT_TIMEZONE-usage-notes)\.

*timestamp*   
A timestamp column or an expression that implicitly converts to a timestamp\.

## Return type<a name="CONVERT_TIMEZONE-return-type"></a>

TIMESTAMP

## Time zone usage notes<a name="CONVERT_TIMEZONE-usage-notes"></a>

Either *source\_timezone* or *target\_timezone* can be specified as a time zone name \(such as 'Africa/Kampala' or 'Singapore'\) or as a time zone abbreviation \(such as 'UTC' or 'PDT'\)\. 

**Note**  
The results of using a time zone name or a time zone abbreviation can be different due to local seasonal time, such as, Daylight Saving Time\. 

To view a list of supported time zone names, execute the following command\. 

```
select pg_timezone_names();
```

 To view a list of supported time zone abbreviations, execute the following command\. 

```
select pg_timezone_abbrevs();
```

### Using a time zone name<a name="CONVERT_TIMEZONE-using-name"></a>

If you specify a time zone using a time zone name, CONVERT\_TIMEZONE automatically adjusts for Daylight Saving Time \(DST\), or any other local seasonal protocol, such as Summer Time, Standard Time, or Winter Time, that is in force for that time zone during the date and time specified by '*timestamp*'\. For example, 'Europe/London' represents UTC in the winter and UTC\+1 in the summer\. 

### Using a time zone abbreviation<a name="CONVERT_TIMEZONE-using-abbrev"></a>

Time zone abbreviations represent a fixed offset from UTC\. If you specify a time zone using a time zone abbreviation, CONVERT\_TIMEZONE uses the fixed offset from UTC and does not adjust for any local seasonal protocol\. For example, ADT \(Atlantic Daylight Time\) always represents UTC\-03, even in winter\. 

### Using POSIX\-style format<a name="CONVERT_TIMEZONE-using-posix"></a>

A POSIX\-style time zone specification is in the form *STDoffset* or *STDoffsetDST*, where *STD* is a time zone abbreviation, *offset* is the numeric offset in hours west from UTC, and *DST* is an optional daylight\-savings zone abbreviation\. Daylight savings time is assumed to be one hour ahead of the given offset\.

POSIX\-style time zone formats use positive offsets west of Greenwich, in contrast to the ISO\-8601 convention, which uses positive offsets east of Greenwich\.

The following are examples of POSIX\-style time zones:
+  PST8
+  PST8PDT
+  EST5
+  EST5EDT

**Note**  
Amazon Redshift doesn't validate POSIX\-style time zone specifications, so it is possible to set the time zone to an invalid value\. For example, the following command doesn't return an error, even though it sets the time zone to an invalid value\.  

```
set timezone to ‘xxx36’;
```

## Examples<a name="CONVERT_TIMEZONE-examples"></a>

The following example converts the timestamp value in the LISTTIME column from the default UTC time zone to PST\. Though the timestamp is within the daylight time period, it's converted to standard time because the target time zone is specified as an abbreviation \(PST\)\.

```
select listtime, convert_timezone('PST', listtime) from listing
where listid = 16;
                     
     listtime       |   convert_timezone
--------------------+-------------------
2008-08-24 09:36:12     2008-08-24 01:36:12
```

The following example converts a timestamp LISTTIME column from the default UTC time zone to US/Pacific time zone\. The target time zone uses a time zone name, and the timestamp is within the daylight time period, so the function returns the daylight time\.

```
select listtime, convert_timezone('US/Pacific', listtime) from listing
where listid = 16;
                     
     listtime       |   convert_timezone
--------------------+---------------------
2008-08-24 09:36:12 | 2008-08-24 02:36:12
```

The following example converts a timestamp string from EST to PST:

```
select convert_timezone('EST', 'PST', '20080305 12:25:29');
                     
 convert_timezone
-------------------
2008-03-05 09:25:29
```

The following example converts a timestamp to US Eastern Standard Time because the target time zone uses a time zone name \(America/New\_York\) and the timestamp is within the standard time period\.

```
select convert_timezone('America/New_York', '2013-02-01 08:00:00');

  convert_timezone
---------------------
2013-02-01 03:00:00
(1 row)
```

The following example converts the timestamp to US Eastern Daylight Time because the target time zone uses a time zone name \(America/New\_York\) and the timestamp is within the daylight time period\.

```
select convert_timezone('America/New_York', '2013-06-01 08:00:00');

  convert_timezone
---------------------
2013-06-01 04:00:00
(1 row)
```

The following example demonstrates the use of offsets\. 

```
SELECT CONVERT_TIMEZONE('GMT','NEWZONE +2','2014-05-17 12:00:00') as newzone_plus_2, 
CONVERT_TIMEZONE('GMT','NEWZONE-2:15','2014-05-17 12:00:00') as newzone_minus_2_15, 
CONVERT_TIMEZONE('GMT','America/Los_Angeles+2','2014-05-17 12:00:00') as la_plus_2,
CONVERT_TIMEZONE('GMT','GMT+2','2014-05-17 12:00:00') as gmt_plus_2;
 
   newzone_plus_2    | newzone_minus_2_15  |      la_plus_2      |     gmt_plus_2
---------------------+---------------------+---------------------+---------------------
2014-05-17 10:00:00 | 2014-05-17 14:15:00 | 2014-05-17 10:00:00 | 2014-05-17 10:00:00
(1 row)
```