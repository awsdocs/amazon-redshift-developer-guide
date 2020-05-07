# Using automatic recognition with DATEFORMAT and TIMEFORMAT<a name="automatic-recognition"></a>

If you specify `'auto'` as the argument for the DATEFORMAT or TIMEFORMAT parameter, Amazon Redshift will automatically recognize and convert the date format or time format in your source data\. The following shows an example\.

```
copy favoritemovies from 'dynamodb://ProductCatalog' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
dateformat 'auto';
```

When used with the `'auto'` argument for DATEFORMAT and TIMEFORMAT, COPY recognizes and converts the date and time formats listed in the table in [ DATEFORMAT and TIMEFORMAT strings](r_DATEFORMAT_and_TIMEFORMAT_strings.md)\. In addition, the `'auto'` argument recognizes the following formats that aren't supported when using a DATEFORMAT and TIMEFORMAT string\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/automatic-recognition.html)

Automatic recognition doesn't support epochsecs and epochmillisecs\.

To test whether a date or timestamp value will be automatically converted, use a CAST function to attempt to convert the string to a date or timestamp value\. For example, the following commands test the timestamp value `'J2345678 04:05:06.789'`:

```
create table formattest (test char(16));
insert into formattest values('J2345678 04:05:06.789');
select test, cast(test as timestamp) as timestamp, cast(test as date) as date from formattest;

        test          |      timestamp      |	date
----------------------+---------------------+------------
J2345678 04:05:06.789   1710-02-23 04:05:06	1710-02-23
```

If the source data for a DATE column includes time information, the time component is truncated\. If the source data for a TIMESTAMP column omits time information, 00:00:00 is used for the time component\.