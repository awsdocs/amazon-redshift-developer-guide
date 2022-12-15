# DATEFORMAT and TIMEFORMAT strings<a name="r_DATEFORMAT_and_TIMEFORMAT_strings"></a>

The COPY command uses the DATEFORMAT and TIMEFORMAT options to parse date and time values in your source data\. DATEFORMAT and TIMEFORMAT are formatted strings that must match the format of your source data's date and time values\. For example, a COPY command loading source data with the date value `Jan-01-1999` must include the following DATEFORMAT string:

```
COPY ...
            DATETIME AS 'MON-DD-YYYY'
```

For more information on managing COPY data conversions, see [Data conversion parameters](https://docs.aws.amazon.com/redshift/latest/dg/copy-parameters-data-conversion.html)\.

DATEFORMAT and TIMEFORMAT strings can contain datetime separators \(such as '`-`', '`/`', or '`:`'\), as well the datepart and timepart formats in the following table\.

**Note**  
If you can't match the format of your date or time values with the following dateparts and timeparts, or if you have date and time values that use formats different from each other, use the `'auto'` argument with the DATEFORMAT or TIMEFORMAT parameter\. The `'auto'` argument recognizes several formats that aren't supported when using a DATEFORMAT or TIMEFORMAT string\. For more information, see [Using automatic recognition with DATEFORMAT and TIMEFORMAT](automatic-recognition.md)\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_DATEFORMAT_and_TIMEFORMAT_strings.html)

The default date format is YYYY\-MM\-DD\. The default timestamp without time zone \(TIMESTAMP\) format is YYYY\-MM\-DD HH:MI:SS\. The default timestamp with time zone \(TIMESTAMPTZ\) format is YYYY\-MM\-DD HH:MI:SSOF, where OF is the offset from UTC \(for example, \-8:00\. You can't include a time zone specifier \(TZ, tz, or OF\) in the timeformat\_string\. The seconds \(SS\) field also supports fractional seconds up to a microsecond level of detail\. To load TIMESTAMPTZ data that is in a format different from the default format, specify 'auto'\.

Following are some sample dates or times you can encounter in your source data, and the corresponding DATEFORMAT or TIMEFORMAT strings for them\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_DATEFORMAT_and_TIMEFORMAT_strings.html)