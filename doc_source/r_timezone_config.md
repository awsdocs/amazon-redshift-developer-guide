# timezone<a name="r_timezone_config"></a>

## Values \(default in bold\)<a name="r_timezone_config-values"></a>

 **UTC**, time zone

## Syntax<a name="r_timezone_config-syntax"></a>

```
SET timezone { TO | = } [ time_zone | DEFAULT ]

SET time zone [ time_zone | DEFAULT ]
```

## Description<a name="r_timezone_config-description"></a>

Sets the time zone for the current session\. The time zone can be the offset from Coordinated Universal Time \(UTC\) or a time zone name\. 

**Note**  
You can't set the `timezone` configuration parameter by using a cluster parameter group\. The time zone can be set only for the current session by using a SET command\. To set the time zone for all sessions run by a specific database user, use the [ALTER USER](r_ALTER_USER.md) command\. ALTER USER … SET TIMEZONE changes the time zone for subsequent sessions, not for the current session\.

When you set the time zone using the `SET timezone` \(one word\) command with either `TO` or `=`, you can specify *time\_zone* as a time zone name, a POSIX\-style format offset, or an ISO\-8601 format offset, as shown following\.

```
SET timezone { TO | = } time_zone
```

When you set the time zone using the SET time zone command *without* `TO` or `=`, you can specify *time\_zone* using an INTERVAL as well as a time zone name, a POSIX\-style format offset, or an ISO\-8601 format offset, as shown following\.

```
SET time zone time_zone
```

## Time zone formats<a name="r_timezone_config-formats"></a>

Amazon Redshift supports the following time zone formats:
+ Time zone name
+ INTERVAL
+ POSIX\-style time zone specification
+ ISO\-8601 offset

Because time zone abbreviations, such as PST or PDT, are defined as a fixed offset from UTC and don't include daylight savings time rules, the SET command doesn't support time zone abbreviations\.

For more details on time zone formats, see the following\.

**Time zone name** – The full time zone name, such as America/New\_York\. Full time zone names can include daylight savings rules\.

The following are examples of time zone names:
+ Etc/Greenwich
+ America/New\_York 
+ CST6CDT
+ GB

**Note**  
Many time zone names, such as EST, MST, NZ, and UCT, are also abbreviations\.

To view a list of valid time zone names, run the following command\.

```
select pg_timezone_names();
```

**INTERVAL** – An offset from UTC\. For example, PST is –8:00 or –8 hours\.

The following are examples of INTERVAL time zone offsets:
+ –8:00
+ –8 hours
+ 30 minutes

**POSIX\-style format** – A time zone specification in the form *STDoffset* or *STDoffsetDST*, where *STD* is a time zone abbreviation, *offset* is the numeric offset in hours west from UTC, and *DST* is an optional daylight\-savings zone abbreviation\. Daylight savings time is assumed to be one hour ahead of the given offset\.

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

**ISO\-8601 Offset** – The offset from UTC in the form `±[hh]:[mm]`\.

The following are examples of ISO\-8601 offsets:
+ \-8:00
+ \+7:30

## Examples<a name="r_timezone_config-examples"></a>

The following example sets the time zone for the current session to New York\.

```
set timezone = 'America/New_York';
```

The following example sets the time zone for the current session to UTC–8 \(PST\)\.

```
set timezone to '-8:00';
```

The following example uses INTERVAL to set the time zone to PST\.

```
set timezone interval '-8 hours'
```

The following example resets the time zone for the current session to the system default time zone \(UTC\)\.

```
set timezone to default;
```

To set the time zone for database user, use an ALTER USER … SET statement\. The following example sets the time zone for dbuser to New York\. The new value persists for the user for all subsequent sessions\. 

```
ALTER USER dbuser SET timezone to 'America/New_York';
```