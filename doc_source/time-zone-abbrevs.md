# Time zone abbreviations<a name="time-zone-abbrevs"></a>

The following SQL statement returns the valid time zone abbreviations that can be specified with the [CONVERT\_TIMEZONE function](CONVERT_TIMEZONE.md)\. 

```
select pg_timezone_abbrevs();
```

The results contain a comma\-separated string with the time zone abbreviation, UTC offset, and indicator if the time zone observes daylight\-savings \(`t` or `f`\)\. For example, the following snippet shows two resulting rows\. The first row contains the abbreviation for Pacific Daylight Time `PDT`, with a `-07:00:00` offset from UTC, and `t` to indicate it observes daylight\-savings time\. The second row contains the abbreviation for Pacific Standard Time `PST`, with a `-08:00:00` offset from UTC, and `f` to indicate it does not observe daylight\-savings time\.

```
pg_timezone_abbrevs
--------------------
(PDT,-07:00:00,t)	
(PST,-08:00:00,f)
```

Run the SQL statement to obtain the entire list and find an abbreviation based on its offset and daylight\-savings indicator\. Approximately 200 rows are returned\.