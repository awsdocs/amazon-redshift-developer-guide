# Time zone names<a name="time-zone-names"></a>

The following SQL statement returns the valid time zone names that can be specified with the [CONVERT\_TIMEZONE function](CONVERT_TIMEZONE.md)\. For a current, complete list of time zone names, run the following command\.

```
select pg_timezone_names();
```

Each row contains a comma\-separated string with the time zone name, abbreviation, UTC offset, and indicator if the time zone observes daylight\-savings \(`t` or `f`\)\. For example, the following snippet shows two resulting rows\. The first row is the time zone `Europe/Paris`, abbreviation `CET`, with `01:00:00` offset from UTC, and `f` to indicate it does not observe daylight\-savings time\. The second row is the time zone `Israel`, abbreviation `IST`, with `02:00:00` offset from UTC, and `f` to indicate it does not observe daylight\-savings time\.

```
pg_timezone_names
-----------------
(Europe/Paris,CET,01:00:00,f)	
(Israel,IST,02:00:00,f)
```

Run the SQL statement to obtain the entire list and find a time zone name\. Approximately 600 rows are returned\. Even though some of the returned time zone names are capitalized initialisms or acronyms \(for example; GB, PRC, ROK\), the CONVERT\_TIMEZONE function treats them as time zone names, not time zone abbreviations\. 