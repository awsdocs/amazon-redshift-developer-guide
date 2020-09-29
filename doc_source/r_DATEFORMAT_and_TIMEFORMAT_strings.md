# DATEFORMAT and TIMEFORMAT strings<a name="r_DATEFORMAT_and_TIMEFORMAT_strings"></a>

The DATEFORMAT and TIMEFORMAT options in the COPY command take format strings\. These strings can contain datetime separators \(such as '`-`', '`/`', or '`:`'\) and the following "dateparts" and "timeparts"\. 

**Note**  
If the COPY command doesn't recognize the format of your date or time values, or if your date and time values use formats different from each other, use the `'auto'` argument with the TIMEFORMAT parameter\. The `'auto'` argument recognizes several formats that aren't supported when using a DATEFORMAT and TIMEFORMAT string\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_DATEFORMAT_and_TIMEFORMAT_strings.html)

The default date format is YYYY\-MM\-DD\. The default timestamp without time zone \(TIMESTAMP\) format is YYYY\-MM\-DD HH:MI:SS\. The default timestamp with time zone \(TIMESTAMPTZ\) format is YYYY\-MM\-DD HH:MI:SSOF, where OF is the offset from UTC \(for example, \-8:00\. You can't include a time zone specifier \(TZ, tz, or OF\) in the timeformat\_string\. The seconds \(SS\) field also supports fractional seconds up to a microsecond level of detail\. To load TIMESTAMPTZ data that is in a format different from the default format, specify 'auto'\. For more information, see [Using automatic recognition with DATEFORMAT and TIMEFORMAT](automatic-recognition.md)\. 

For example, the following DATEFORMAT and TIMEFORMAT strings are valid\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_DATEFORMAT_and_TIMEFORMAT_strings.html)