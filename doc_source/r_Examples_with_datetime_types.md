# Examples with datetime types<a name="r_Examples_with_datetime_types"></a>

Following, you can find examples for working with datetime types supported by Amazon Redshift\.

## Date examples<a name="r_Examples_with_datetime_types-date-examples"></a>

The following examples insert dates that have different formats and display the output\. 

```
create table datetable (start_date date, end_date date);
```

```
insert into datetable values ('2008-06-01','2008-12-31');

insert into datetable values ('Jun 1,2008','20081231');
```

```
select * from datetable order by 1;

start_date |  end_date
------------------------
2008-06-01 | 2008-12-31
2008-06-01 | 2008-12-31
```

If you insert a timestamp value into a DATE column, the time portion is ignored and only the date is loaded\.

## Time stamp examples<a name="r_Examples_with_datetime_types-timestamp-examples"></a>

If you insert a date into a TIMESTAMP or TIMESTAMPTZ column, the time defaults to midnight\. For example, if you insert the literal `20081231`, the stored value is `2008-12-31 00:00:00`\. 

To change the time zone for the current session, use the [SET](r_SET.md) command to set the [timezone](r_timezone_config.md) configuration parameter\.

The following examples insert timestamps that have different formats and display the output\. 

```
create table tstamp(timeofday timestamp, timeofdaytz timestamptz);

insert into tstamp values('Jun 1,2008  09:59:59', 'Jun 1,2008 09:59:59 EST' );
insert into tstamp values('Dec 31,2008 18:20','Dec 31,2008 18:20');
insert into tstamp values('Jun 1,2008  09:59:59 EST', 'Jun 1,2008 09:59:59');

timeofday
---------------------
2008-06-01 09:59:59
2008-12-31 18:20:00
(2 rows)
```