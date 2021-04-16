# UNLOAD examples<a name="r_UNLOAD_command_examples"></a>

## Unload VENUE to a pipe\-delimited file \(default delimiter\)<a name="unload-examples-venue"></a>

**Note**  
These examples contain line breaks for readability\. Do not include line breaks or spaces in your *credentials\-args* string\.

The following example unloads the VENUE table and writes the data to `s3://mybucket/unload/`: 

```
unload ('select * from venue')
to 's3://mybucket/unload/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

By default, UNLOAD writes one or more files per slice\. Assuming a two\-node cluster with two slices per node, the previous example creates these files in `mybucket`:

```
unload/0000_part_00
unload/0001_part_00
unload/0002_part_00
unload/0003_part_00
```

To better differentiate the output files, you can include a prefix in the location\. The following example unloads the VENUE table and writes the data to `s3://mybucket/venue_pipe_`: 

```
unload ('select * from venue')
to 's3://mybucket/unload/venue_pipe_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The result is these four files in the `unload` folder, again assuming four slices\.

```
venue_pipe_0000_part_00
venue_pipe_0001_part_00
venue_pipe_0002_part_00
venue_pipe_0003_part_00
```

## Unload LINEITEM table to partitioned Parquet files<a name="unload-examples-partitioned-parquet"></a>

The following example unloads the LINEITEM table in Parquet format, partitioned by the `l_shipdate` column\. 

```
unload ('select * from lineitem')
to 's3://mybucket/lineitem/'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
PARQUET
PARTITION BY (l_shipdate);
```

Assuming four slices, the resulting Parquet files are dynamically partitioned into various folders\. 

```
s3://mybucket/lineitem/l_shipdate=1992-01-02/0000_part_00.parquet
                                             0001_part_00.parquet
                                             0002_part_00.parquet
                                             0003_part_00.parquet
s3://mybucket/lineitem/l_shipdate=1992-01-03/0000_part_00.parquet
                                             0001_part_00.parquet
                                             0002_part_00.parquet
                                             0003_part_00.parquet
s3://mybucket/lineitem/l_shipdate=1992-01-04/0000_part_00.parquet
                                             0001_part_00.parquet
                                             0002_part_00.parquet
                                             0003_part_00.parquet
...
```

**Note**  
In some cases, the UNLOAD command used the INCLUDE option as shown in the following SQL statement\.   

```
unload ('select * from lineitem')
to 's3://mybucket/lineitem/'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
PARQUET
PARTITION BY (l_shipdate) INCLUDE;
```
In these cases, the `l_shipdate` column is also in the data in the Parquet files\. Otherwise, the `l_shipdate` column data isn't in the Parquet files\.

## Unload VENUE to a CSV file<a name="unload-examples-csv"></a>

The following example unloads the VENUE table and writes the data in CSV format to `s3://mybucket/unload/`\.

```
unload ('select * from venue')
to 's3://mybucket/unload/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
CSV;
```

Suppose that the VENUE table contains the following rows\.

```
venueid | venuename                  | venuecity       | venuestate | venueseats
--------+----------------------------+-----------------+------------+-----------
      1 | Pinewood Racetrack         | Akron           | OH         | 0
      2 | Columbus "Crew" Stadium    | Columbus        | OH         | 0
      4 | Community, Ballpark, Arena | Kansas City     | KS         | 0
```

The unload file looks similar to the following\.

```
1,Pinewood Racetrack,Akron,OH,0
2,"Columbus ""Crew"" Stadium",Columbus,OH,0
4,"Community, Ballpark, Arena",Kansas City,KS,0
```

## Unload VENUE to a CSV file using a delimiter<a name="unload-examples-csv-delimiter"></a>

The following example unloads the VENUE table and writes the data in CSV format using the pipe character \(\|\) as the delimiter\. The unloaded file is written to `s3://mybucket/unload/`\. The VENUE table in this example contains the pipe character in the value of the first row \(`Pinewood Race|track`\)\. It does this to show that the value in the result is enclosed in double quotation marks\. A double quotation mark is escaped by a double quotation mark, and the entire field is enclosed in double quotation marks\. 

```
unload ('select * from venue')
to 's3://mybucket/unload/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
CSV DELIMITER AS '|';
```

Suppose that the VENUE table contains the following rows\.

```
venueid | venuename                  | venuecity       | venuestate | venueseats
--------+----------------------------+-----------------+------------+-------------
      1 | Pinewood Race|track        | Akron           | OH         | 0
      2 | Columbus "Crew" Stadium    | Columbus        | OH         | 0
      4 | Community, Ballpark, Arena | Kansas City     | KS         | 0
```

The unload file looks similar to the following\.

```
1|"Pinewood Race|track"|Akron|OH|0
2|"Columbus ""Crew"" Stadium"|Columbus|OH|0
4|Community, Ballpark, Arena|Kansas City|KS|0
```

## Unload VENUE with a manifest file<a name="unload-examples-manifest"></a>

To create a manifest file, include the MANIFEST option\. The following example unloads the VENUE table and writes a manifest file along with the data files to s3://mybucket/venue\_pipe\_: 

**Important**  
If you unload files with the MANIFEST option, you should use the MANIFEST option with the COPY command when you load the files\. If you use the same prefix to load the files and don't specify the MANIFEST option, COPY fails because it assumes the manifest file is a data file\.

```
unload ('select * from venue')
to 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest;
```

The result is these five files:

```
s3://mybucket/venue_pipe_0000_part_00 
s3://mybucket/venue_pipe_0001_part_00 
s3://mybucket/venue_pipe_0002_part_00 
s3://mybucket/venue_pipe_0003_part_00
s3://mybucket/venue_pipe_manifest
```

The following shows the contents of the manifest file\. 

```
{
  "entries": [
    {"url":"s3://mybucket/tickit/venue_0000_part_00"},
    {"url":"s3://mybucket/tickit/venue_0001_part_00"},
    {"url":"s3://mybucket/tickit/venue_0002_part_00"},
    {"url":"s3://mybucket/tickit/venue_0003_part_00"}
  ]
}
```

## Unload VENUE with MANIFEST VERBOSE<a name="unload-examples-manifest-verbose"></a>

When you specify the MANIFEST VERBOSE option, the manifest file includes the following sections: 
+ The `entries` section lists Amazon S3 path, file size, and row count for each file\. 
+ The `schema` section lists the column names, data types, and dimension for each column\. 
+ The `meta` section shows the total file size and row count for all files\. 

The following example unloads the VENUE table using the MANIFEST VERBOSE option\. 

```
unload ('select * from venue')
to 's3://mybucket/unload_venue_folder/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest verbose;
```

The following shows the contents of the manifest file\.

```
{
  "entries": [
    {"url":"s3://mybucket/venue_pipe_0000_part_00", "meta": { "content_length": 32295, "record_count": 10 }},
    {"url":"s3://mybucket/venue_pipe_0001_part_00", "meta": { "content_length": 32771, "record_count": 20 }},
    {"url":"s3://mybucket/venue_pipe_0002_part_00", "meta": { "content_length": 32302, "record_count": 10 }},
    {"url":"s3://mybucket/venue_pipe_0003_part_00", "meta": { "content_length": 31810, "record_count": 15 }}
  ],
  "schema": {
    "elements": [
      {"name": "venueid", "type": { "base": "integer" }},
      {"name": "venuename", "type": { "base": "character varying", 25 }},
      {"name": "venuecity", "type": { "base": "character varying", 25 }},
      {"name": "venuestate", "type": { "base": "character varying", 25 }},
      {"name": "venueseats", "type": { "base": "character varying", 25 }}
    ]
  },
  "meta": {
    "content_length": 129178,
    "record_count": 55
  },
  "author": {
    "name": "Amazon Redshift",
    "version": "1.0.0"
  }
}
```

## Unload VENUE with a header<a name="unload-examples-header"></a>

The following example unloads VENUE with a header row\.

```
unload ('select * from venue where venueseats > 75000')
to 's3://mybucket/unload/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
header
parallel off;
```

The following shows the contents of the output file with a header row\.

```
venueid|venuename|venuecity|venuestate|venueseats
6|New York Giants Stadium|East Rutherford|NJ|80242
78|INVESCO Field|Denver|CO|76125
83|FedExField|Landover|MD|91704
79|Arrowhead Stadium|Kansas City|MO|79451
```

## Unload VENUE to smaller files<a name="unload-examples-maxfilesize"></a>

By default, the maximum file size is 6\.2 GB\. If the unload data is larger than 6\.2 GB, UNLOAD creates a new file for each 6\.2 GB data segment\. To create smaller files, include the MAXFILESIZE parameter\. Assuming the size of the data in the previous example was 20 GB, the following UNLOAD command creates 20 files, each 1 GB in size\.

```
unload ('select * from venue')
to 's3://mybucket/unload/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
maxfilesize 1 gb;
```

## Unload VENUE serially<a name="unload-examples-serial"></a>

To unload serially, specify PARALLEL OFF\. UNLOAD then writes one file at a time, up to a maximum of 6\.2 GB per file\. 

The following example unloads the VENUE table and writes the data serially to `s3://mybucket/unload/`\. 

```
unload ('select * from venue')
to 's3://mybucket/unload/venue_serial_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
parallel off;
```

The result is one file named venue\_serial\_000\. 

If the unload data is larger than 6\.2 GB, UNLOAD creates a new file for each 6\.2 GB data segment\. The following example unloads the LINEORDER table and writes the data serially to `s3://mybucket/unload/`\. 

```
unload ('select * from lineorder')
to 's3://mybucket/unload/lineorder_serial_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
parallel off gzip;
```

The result is the following series of files\.

```
lineorder_serial_0000.gz
lineorder_serial_0001.gz
lineorder_serial_0002.gz
lineorder_serial_0003.gz
```

To better differentiate the output files, you can include a prefix in the location\. The following example unloads the VENUE table and writes the data to `s3://mybucket/venue_pipe_`: 

```
unload ('select * from venue')
to 's3://mybucket/unload/venue_pipe_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The result is these four files in the `unload` folder, again assuming four slices\.

```
venue_pipe_0000_part_00
venue_pipe_0001_part_00
venue_pipe_0002_part_00
venue_pipe_0003_part_00
```

## Load VENUE from unload files<a name="unload-examples-load"></a>

To load a table from a set of unload files, simply reverse the process by using a COPY command\. The following example creates a new table, LOADVENUE, and loads the table from the data files created in the previous example\.

```
create table loadvenue (like venue);

copy loadvenue from 's3://mybucket/venue_pipe_' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

If you used the MANIFEST option to create a manifest file with your unload files, you can load the data using the same manifest file\. You do so with a COPY command with the MANIFEST option\. The following example loads data using a manifest file\.

```
copy loadvenue 
from 's3://mybucket/venue_pipe_manifest' iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest;
```

## Unload VENUE to encrypted files<a name="unload-examples-unload-encrypted"></a>

The following example unloads the VENUE table to a set of encrypted files using a KMS key\. If you specify a manifest file with the ENCRYPTED option, the manifest file is also encrypted\. For more information, see [Unloading encrypted data files](t_unloading_encrypted_files.md)\.

```
unload ('select * from venue')
to 's3://mybucket/venue_encrypt_kms' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
kms_key_id '1234abcd-12ab-34cd-56ef-1234567890ab' 
manifest
encrypted;
```

The following example unloads the VENUE table to a set of encrypted files using a master symmetric key\. 

```
unload ('select * from venue')
to 's3://mybucket/venue_encrypt_cmk' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key 'EXAMPLEMASTERKEYtkbjk/OpCwtYSx/M4/t7DMCDIK722' 
encrypted;
```

## Load VENUE from encrypted files<a name="unload-examples-load-encrypted"></a>

To load tables from a set of files that were created by using UNLOAD with the ENCRYPT option, reverse the process by using a COPY command\. With that command, use the ENCRYPTED option and specify the same master symmetric key that was used for the UNLOAD command\. The following example loads the LOADVENUE table from the encrypted data files created in the previous example\.

```
create table loadvenue (like venue);

copy loadvenue 
from 's3://mybucket/venue_encrypt_manifest' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
master_symmetric_key 'EXAMPLEMASTERKEYtkbjk/OpCwtYSx/M4/t7DMCDIK722' 
manifest
encrypted;
```

## Unload VENUE data to a tab\-delimited file<a name="unload-examples-venue-tab"></a>

```
unload ('select venueid, venuename, venueseats from venue')
to 's3://mybucket/venue_tab_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter as '\t';
```

The output data files look like this: 

```
1	Toyota Park	Bridgeview	IL	0
2	Columbus Crew Stadium	Columbus	OH	0
3	RFK Stadium	Washington	DC	0
4	CommunityAmerica Ballpark	Kansas City	KS	0
5	Gillette Stadium	Foxborough	MA	68756
...
```

## Unload VENUE using temporary credentials<a name="unload-venue-using-temporary-credentials"></a>

You can limit the access users have to your data by using temporary security credentials\. Temporary security credentials provide enhanced security because they have short life spans and can't be reused after they expire\. A user who has these temporary security credentials can access your resources only until the credentials expire\. For more information, see [Temporary security credentials](copy-usage_notes-access-permissions.md#r_copy-temporary-security-credentials) in the usage notes for the COPY command\.

The following example unloads the LISTING table using temporary credentials:

```
unload ('select venueid, venuename, venueseats from venue')
to 's3://mybucket/venue_tab' credentials 
'aws_access_key_id=<temporary-access-key-id>;aws_secret_access_key=<temporary-secret-access-key>;token=<temporary-token>'
delimiter as '\t';
```

**Important**  
The temporary security credentials must be valid for the entire duration of the UNLOAD statement\. If the temporary security credentials expire during the load process, the UNLOAD fails and the transaction is rolled back\. For example, if temporary security credentials expire after 15 minutes and the UNLOAD requires one hour, the UNLOAD fails before it completes\.

## Unload VENUE to a fixed\-width data file<a name="unload-venue-fixed-width"></a>

```
unload ('select * from venue')
to 's3://mybucket/venue_fw_'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
fixedwidth as 'venueid:3,venuename:39,venuecity:16,venuestate:2,venueseats:6';
```

The output data files look like the following\. 

```
1  Toyota Park              Bridgeview  IL0
2  Columbus Crew Stadium    Columbus    OH0
3  RFK Stadium              Washington  DC0
4  CommunityAmerica BallparkKansas City KS0
5  Gillette Stadium         Foxborough  MA68756
...
```

## Unload VENUE to a set of tab\-delimited GZIP\-compressed files<a name="unload-examples-venue-gzip"></a>

```
unload ('select * from venue') 
to 's3://mybucket/venue_tab_'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter as '\t' 
gzip;
```

## Unload data that contains a delimiter<a name="unload-examples-delimiter"></a>

This example uses the ADDQUOTES option to unload comma\-delimited data where some of the actual data fields contain a comma\.

First, create a table that contains quotation marks\.

```
create table location (id int, location char(64));

insert into location values (1,'Phoenix, AZ'),(2,'San Diego, CA'),(3,'Chicago, IL');
```

Then, unload the data using the ADDQUOTES option\.

```
unload ('select id, location from location') 
to 's3://mybucket/location_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter ',' addquotes;
```

The unloaded data files look like this: 

```
1,"Phoenix, AZ"
2,"San Diego, CA"
3,"Chicago, IL" 
...
```

## Unload the results of a join query<a name="unload-examples-join"></a>

The following example unloads the results of a join query that contains a window function\. 

```
unload ('select venuecity, venuestate, caldate, pricepaid,
sum(pricepaid) over(partition by venuecity, venuestate
order by caldate rows between 3 preceding and 3 following) as winsum
from sales join date on sales.dateid=date.dateid 
join event on event.eventid=sales.eventid
join venue on event.venueid=venue.venueid
order by 1,2')
to 's3://mybucket/tickit/winsum' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The output files look like this: 

```
Atlanta|GA|2008-01-04|363.00|1362.00
Atlanta|GA|2008-01-05|233.00|2030.00
Atlanta|GA|2008-01-06|310.00|3135.00
Atlanta|GA|2008-01-08|166.00|8338.00
Atlanta|GA|2008-01-11|268.00|7630.00
...
```

## Unload using NULL AS<a name="unload-examples-null-as"></a>

UNLOAD outputs null values as empty strings by default\. The following examples show how to use NULL AS to substitute a text string for nulls\.

For these examples, we add some null values to the VENUE table\.

```
update venue set venuestate = NULL
where venuecity = 'Cleveland';
```

Select from VENUE where VENUESTATE is null to verify that the columns contain NULL\.

```
select * from venue where venuestate is null;

 venueid |        venuename         | venuecity | venuestate | venueseats
---------+--------------------------+-----------+------------+------------
      22 | Quicken Loans Arena      | Cleveland |            |          0
     101 | Progressive Field        | Cleveland |            |      43345
      72 | Cleveland Browns Stadium | Cleveland |            |      73200
(3 rows)
```

Now, UNLOAD the VENUE table using the NULL AS option to replace null values with the character string '`fred`'\. 

```
unload ('select * from venue') 
to 's3://mybucket/nulls/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
null as 'fred';
```

The following sample from the unload file shows that null values were replaced with `fred`\. It turns out that some values for VENUESEATS were also null and were replaced with `fred`\. Even though the data type for VENUESEATS is integer, UNLOAD converts the values to text in the unload files, and then COPY converts them back to integer\. If you are unloading to a fixed\-width file, the NULL AS string must not be larger than the field width\.

```
248|Charles Playhouse|Boston|MA|0
251|Paris Hotel|Las Vegas|NV|fred
258|Tropicana Hotel|Las Vegas|NV|fred
300|Kennedy Center Opera House|Washington|DC|0
306|Lyric Opera House|Baltimore|MD|0
308|Metropolitan Opera|New York City|NY|0
  5|Gillette Stadium|Foxborough|MA|5
 22|Quicken Loans Arena|Cleveland|fred|0
101|Progressive Field|Cleveland|fred|43345
...
```

To load a table from the unload files, use a COPY command with the same NULL AS option\. 

**Note**  
If you attempt to load nulls into a column defined as NOT NULL, the COPY command fails\.

```
create table loadvenuenulls (like venue);

copy loadvenuenulls from 's3://mybucket/nulls/'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
null as 'fred';
```

To verify that the columns contain null, not just empty strings, select from LOADVENUENULLS and filter for null\.

```
select * from loadvenuenulls where venuestate is null or venueseats is null;

 venueid |        venuename         | venuecity | venuestate | venueseats
---------+--------------------------+-----------+------------+------------
      72 | Cleveland Browns Stadium | Cleveland |            |      73200
     253 | Mirage Hotel             | Las Vegas | NV         |
     255 | Venetian Hotel           | Las Vegas | NV         |
      22 | Quicken Loans Arena      | Cleveland |            |          0
     101 | Progressive Field        | Cleveland |            |      43345
     251 | Paris Hotel              | Las Vegas | NV         |

...
```

You can UNLOAD a table that contains nulls using the default NULL AS behavior and then COPY the data back into a table using the default NULL AS behavior; however, any non\-numeric fields in the target table contain empty strings, not nulls\. By default UNLOAD converts nulls to empty strings \(white space or zero\-length\)\. COPY converts empty strings to NULL for numeric columns, but inserts empty strings into non\-numeric columns\. The following example shows how to perform an UNLOAD followed by a COPY using the default NULL AS behavior\. 

```
unload ('select * from venue') 
to 's3://mybucket/nulls/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' allowoverwrite;

truncate loadvenuenulls;
copy loadvenuenulls from 's3://mybucket/nulls/'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

In this case, when you filter for nulls, only the rows where VENUESEATS contained nulls\. Where VENUESTATE contained nulls in the table \(VENUE\), VENUESTATE in the target table \(LOADVENUENULLS\) contains empty strings\.

```
select * from loadvenuenulls where venuestate is null or venueseats is null;

 venueid |        venuename         | venuecity | venuestate | venueseats
---------+--------------------------+-----------+------------+------------
     253 | Mirage Hotel             | Las Vegas | NV         |
     255 | Venetian Hotel           | Las Vegas | NV         |
     251 | Paris Hotel              | Las Vegas | NV         |
...
```

To load empty strings to non\-numeric columns as NULL, include the EMPTYASNULL or BLANKSASNULL options\. It's OK to use both\.

```
unload ('select * from venue') 
to 's3://mybucket/nulls/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' allowoverwrite;

truncate loadvenuenulls;
copy loadvenuenulls from 's3://mybucket/nulls/'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' EMPTYASNULL;
```

To verify that the columns contain NULL, not just whitespace or empty, select from LOADVENUENULLS and filter for null\.

```
select * from loadvenuenulls where venuestate is null or venueseats is null;

 venueid |        venuename         | venuecity | venuestate | venueseats
---------+--------------------------+-----------+------------+------------
      72 | Cleveland Browns Stadium | Cleveland |            |      73200
     253 | Mirage Hotel             | Las Vegas | NV         |
     255 | Venetian Hotel           | Las Vegas | NV         |
      22 | Quicken Loans Arena      | Cleveland |            |          0
     101 | Progressive Field        | Cleveland |            |      43345
     251 | Paris Hotel              | Las Vegas | NV         |
...
```

## ALLOWOVERWRITE example<a name="unload-examples-allowoverwrite"></a>

By default, UNLOAD doesn't overwrite existing files in the destination bucket\. For example, if you run the same UNLOAD statement twice without modifying the files in the destination bucket, the second UNLOAD fails\. To overwrite the existing files, including the manifest file, specify the ALLOWOVERWRITE option\.

```
unload ('select * from venue')
to 's3://mybucket/venue_pipe_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest allowoverwrite;
```