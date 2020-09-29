# COPY examples<a name="r_COPY_command_examples"></a>

**Note**  
These examples contain line breaks for readability\. Do not include line breaks or spaces in your *credentials\-args* string\.

**Topics**
+ [Load FAVORITEMOVIES from an DynamoDB table](#r_COPY_command_examples-load-favoritemovies-from-an-amazon-dynamodb-table)
+ [Load LISTING from an Amazon S3 bucket](#r_COPY_command_examples-load-listing-from-an-amazon-s3-bucket)
+ [Load LISTING from an Amazon EMR cluster](#copy-command-examples-emr)
+ [Example: COPY from Amazon S3 using a manifest](#copy-command-examples-manifest)
+ [Load LISTING from a pipe\-delimited file \(default delimiter\)](#r_COPY_command_examples-load-listing-from-a-pipe-delimited-file-default-delimiter)
+ [Load LISTING using columnar data in Parquet format](#r_COPY_command_examples-load-listing-from-parquet)
+ [Load LISTING using temporary credentials](#sub-example-load-favorite-movies)
+ [Load EVENT with options](#r_COPY_command_examples-load-event-with-options)
+ [Load VENUE from a fixed\-width data file](#r_COPY_command_examples-load-venue-from-a-fixed-width-data-file)
+ [Load CATEGORY from a CSV file](#load-from-csv)
+ [Load VENUE with explicit values for an IDENTITY column](#r_COPY_command_examples-load-venue-with-explicit-values-for-an-identity-column)
+ [Load TIME from a pipe\-delimited GZIP file](#r_COPY_command_examples-load-time-from-a-pipe-delimited-gzip-file)
+ [Load a timestamp or datestamp](#r_COPY_command_examples-load-a-time-datestamp)
+ [Load data from a file with default values](#r_COPY_command_examples-load-data-from-a-file-with-default-values)
+ [COPY data with the ESCAPE option](#r_COPY_command_examples-copy-data-with-the-escape-option)
+ [Copy from JSON examples](#r_COPY_command_examples-copy-from-json)
+ [Copy from Avro examples](#r_COPY_command_examples-copy-from-avro)
+ [Preparing files for COPY with the ESCAPE option](#r_COPY_preparing_data)
+ [Loading a shapefile into Amazon Redshift](#copy-example-spatial-copy-shapefile)

## Load FAVORITEMOVIES from an DynamoDB table<a name="r_COPY_command_examples-load-favoritemovies-from-an-amazon-dynamodb-table"></a>

The AWS SDKs include a simple example of creating a DynamoDB table called *Movies*\. \(For this example, see [Getting Started with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.html)\.\) The following example loads the Amazon Redshift MOVIES table with data from the DynamoDB table\. The Amazon Redshift table must already exist in the database\.

```
copy favoritemovies from 'dynamodb://Movies'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
readratio 50;
```

## Load LISTING from an Amazon S3 bucket<a name="r_COPY_command_examples-load-listing-from-an-amazon-s3-bucket"></a>

The following example loads LISTING from an Amazon S3 bucket\. The COPY command loads all of the files in the `/data/listing/` folder\.

```
copy listing
from 's3://mybucket/data/listing/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

## Load LISTING from an Amazon EMR cluster<a name="copy-command-examples-emr"></a>

The following example loads the SALES table with tab\-delimited data from lzop\-compressed files in an Amazon EMR cluster\. COPY loads every file in the `myoutput/` folder that begins with `part-`\.

```
copy sales
from 'emr://j-SAMPLE2B500FC/myoutput/part-*' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '\t' lzop;
```

The following example loads the SALES table with JSON formatted data in an Amazon EMR cluster\. COPY loads every file in the `myoutput/json/` folder\.

```
copy sales
from 'emr://j-SAMPLE2B500FC/myoutput/json/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
JSON 's3://mybucket/jsonpaths.txt';
```

## Using a manifest to specify data files<a name="copy-command-examples-manifest"></a>

You can use a manifest to ensure that your COPY command loads all of the required files, and only the required files, from Amazon S3\. You can also use a manifest when you need to load multiple files from different buckets or files that don't share the same prefix\. 

For example, suppose that you need to load the following three files: `custdata1.txt`, `custdata2.txt`, and `custdata3.txt`\. You could use the following command to load all of the files in `mybucket` that begin with `custdata` by specifying a prefix: 

```
copy category
from 's3://mybucket/custdata' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

If only two of the files exist because of an error, COPY loads only those two files and finishes successfully, resulting in an incomplete data load\. If the bucket also contains an unwanted file that happens to use the same prefix, such as a file named `custdata.backup` for example, COPY loads that file as well, resulting in unwanted data being loaded\.

To ensure that all of the required files are loaded and to prevent unwanted files from being loaded, you can use a manifest file\. The manifest is a JSON\-formatted text file that lists the files to be processed by the COPY command\. For example, the following manifest loads the three files in the previous example\.

```
{  
   "entries":[  
      {  
         "url":"s3://mybucket/custdata.1",
         "mandatory":true
      },
      {  
         "url":"s3://mybucket/custdata.2",
         "mandatory":true
      },
      {  
         "url":"s3://mybucket/custdata.3",
         "mandatory":true
      }
   ]
}
```

The optional `mandatory` flag indicates whether COPY should terminate if the file doesn't exist\. The default is `false`\. Regardless of any mandatory settings, COPY terminates if no files are found\. In this example, COPY returns an error if any of the files isn't found\. Unwanted files that might have been picked up if you specified only a key prefix, such as `custdata.backup`, are ignored, because they aren't on the manifest\. 

When loading from data files in ORC or Parquet format, a `meta` field is required, as shown in the following example\.

```
{  
   "entries":[  
      {  
         "url":"s3://mybucket-alpha/orc/2013-10-04-custdata",
         "mandatory":true,
         "meta":{  
            "content_length":99
         }
      },
      {  
         "url":"s3://mybucket-beta/orc/2013-10-05-custdata",
         "mandatory":true,
         "meta":{  
            "content_length":99
         }
      }
   ]
}
```

The following example uses a manifest named `cust.manifest`\. 

```
copy customer
from 's3://mybucket/cust.manifest' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest;
```

You can use a manifest to load files from different buckets or files that don't share the same prefix\. The following example shows the JSON to load data with files whose names begin with a date stamp\.

```
{
  "entries": [
    {"url":"s3://mybucket/2013-10-04-custdata.txt","mandatory":true},
    {"url":"s3://mybucket/2013-10-05-custdata.txt”,"mandatory":true},
    {"url":"s3://mybucket/2013-10-06-custdata.txt”,"mandatory":true},
    {"url":"s3://mybucket/2013-10-07-custdata.txt”,"mandatory":true}
  ]
}
```

The manifest can list files that are in different buckets, as long as the buckets are in the same AWS Region as the cluster\. 

```
{
  "entries": [
    {"url":"s3://mybucket-alpha/custdata1.txt","mandatory":false},
    {"url":"s3://mybucket-beta/custdata1.txt","mandatory":false},
    {"url":"s3://mybucket-beta/custdata2.txt","mandatory":false}
  ]
}
```

## Load LISTING from a pipe\-delimited file \(default delimiter\)<a name="r_COPY_command_examples-load-listing-from-a-pipe-delimited-file-default-delimiter"></a>

The following example is a very simple case in which no options are specified and the input file contains the default delimiter, a pipe character \('\|'\)\. 

```
copy listing 
from 's3://mybucket/data/listings_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

## Load LISTING using columnar data in Parquet format<a name="r_COPY_command_examples-load-listing-from-parquet"></a>

The following example loads data from a folder on Amazon S3 named parquet\. 

```
copy listing 
from 's3://mybucket/data/listings/parquet/' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
format as parquet;
```

## Load LISTING using temporary credentials<a name="sub-example-load-favorite-movies"></a>

The following example uses the SESSION\_TOKEN parameter to specify temporary session credentials:

```
copy listing
from 's3://mybucket/data/listings_pipe.txt'
access_key_id '<access-key-id>'
secret_access_key '<secret-access-key'
session_token '<temporary-token>';
```

## Load EVENT with options<a name="r_COPY_command_examples-load-event-with-options"></a>

The following example loads pipe\-delimited data into the EVENT table and applies the following rules: 
+ If pairs of quotation marks are used to surround any character strings, they are removed\.
+ Both empty strings and strings that contain blanks are loaded as NULL values\.
+ The load fails if more than 5 errors are returned\.
+ Timestamp values must comply with the specified format; for example, a valid timestamp is `2008-09-26 05:43:12`\.

```
copy event
from 's3://mybucket/data/allevents_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
removequotes
emptyasnull
blanksasnull
maxerror 5
delimiter '|'
timeformat 'YYYY-MM-DD HH:MI:SS';
```

## Load VENUE from a fixed\-width data file<a name="r_COPY_command_examples-load-venue-from-a-fixed-width-data-file"></a>

```
copy venue
from 's3://mybucket/data/venue_fw.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
fixedwidth 'venueid:3,venuename:25,venuecity:12,venuestate:2,venueseats:6';
```

The preceding example assumes a data file formatted in the same way as the sample data shown\. In the sample following, spaces act as placeholders so that all of the columns are the same width as noted in the specification: 

```
1  Toyota Park              Bridgeview  IL0
2  Columbus Crew Stadium    Columbus    OH0
3  RFK Stadium              Washington  DC0
4  CommunityAmerica BallparkKansas City KS0
5  Gillette Stadium         Foxborough  MA68756
```

## Load CATEGORY from a CSV file<a name="load-from-csv"></a>

Suppose you want to load the CATEGORY with the values shown in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_COPY_command_examples.html)

The following example shows the contents of a text file with the field values separated by commas\.

```
12,Shows,Musicals,Musical theatre
13,Shows,Plays,All "non-musical" theatre  
14,Shows,Opera,All opera, light, and "rock" opera
15,Concerts,Classical,All symphony, concerto, and choir concerts
```

If you load the file using the DELIMITER parameter to specify comma\-delimited input, the COPY command fails because some input fields contain commas\. You can avoid that problem by using the CSV parameter and enclosing the fields that contain commas in quotation mark characters\. If the quotation mark character appears within a quoted string, you need to escape it by doubling the quotation mark character\. The default quotation mark character is a double quotation mark, so you need to escape each double quotation mark with an additional double quotation mark\. Your new input file looks something like this\. 

```
12,Shows,Musicals,Musical theatre
13,Shows,Plays,"All ""non-musical"" theatre"
14,Shows,Opera,"All opera, light, and ""rock"" opera"
15,Concerts,Classical,"All symphony, concerto, and choir concerts"
```

Assuming the file name is `category_csv.txt`, you can load the file by using the following COPY command:

```
copy category
from 's3://mybucket/data/category_csv.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
csv;
```

Alternatively, to avoid the need to escape the double quotation marks in your input, you can specify a different quotation mark character by using the QUOTE AS parameter\. For example, the following version of `category_csv.txt` uses '`%`' as the quotation mark character\.

```
12,Shows,Musicals,Musical theatre
13,Shows,Plays,%All "non-musical" theatre%
14,Shows,Opera,%All opera, light, and "rock" opera%
15,Concerts,Classical,%All symphony, concerto, and choir concerts%
```

The following COPY command uses QUOTE AS to load `category_csv.txt`:

```
copy category
from 's3://mybucket/data/category_csv.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
csv quote as '%';
```

## Load VENUE with explicit values for an IDENTITY column<a name="r_COPY_command_examples-load-venue-with-explicit-values-for-an-identity-column"></a>

The following example assumes that when the VENUE table was created that at least one column \(such as the `venueid` column\) was specified to be an IDENTITY column\. This command overrides the default IDENTITY behavior of autogenerating values for an IDENTITY column and instead loads the explicit values from the venue\.txt file\.

```
copy venue
from 's3://mybucket/data/venue.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
explicit_ids;
```

## Load TIME from a pipe\-delimited GZIP file<a name="r_COPY_command_examples-load-time-from-a-pipe-delimited-gzip-file"></a>

The following example loads the TIME table from a pipe\-delimited GZIP file:

```
copy time
from 's3://mybucket/data/timerows.gz' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
gzip
delimiter '|';
```

## Load a timestamp or datestamp<a name="r_COPY_command_examples-load-a-time-datestamp"></a>

The following example loads data with a formatted timestamp\.

**Note**  
The TIMEFORMAT of `HH:MI:SS` can also support fractional seconds beyond the `SS` to a microsecond level of detail\. The file `time.txt` used in this example contains one row, `2009-01-12 14:15:57.119568`\.

```
copy timestamp1 
from 's3://mybucket/data/time.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
timeformat 'YYYY-MM-DD HH:MI:SS';
```

The result of this copy is as follows: 

```
select * from timestamp1;
c1
----------------------------
2009-01-12 14:15:57.119568
(1 row)
```

## Load data from a file with default values<a name="r_COPY_command_examples-load-data-from-a-file-with-default-values"></a>

The following example uses a variation of the VENUE table in the TICKIT database\. Consider a VENUE\_NEW table defined with the following statement: 

```
create table venue_new(
venueid smallint not null,
venuename varchar(100) not null,
venuecity varchar(30),
venuestate char(2),
venueseats integer not null default '1000');
```

Consider a venue\_noseats\.txt data file that contains no values for the VENUESEATS column, as shown in the following example: 

```
1|Toyota Park|Bridgeview|IL|
2|Columbus Crew Stadium|Columbus|OH|
3|RFK Stadium|Washington|DC|
4|CommunityAmerica Ballpark|Kansas City|KS|
5|Gillette Stadium|Foxborough|MA|
6|New York Giants Stadium|East Rutherford|NJ|
7|BMO Field|Toronto|ON|
8|The Home Depot Center|Carson|CA|
9|Dick's Sporting Goods Park|Commerce City|CO|
10|Pizza Hut Park|Frisco|TX|
```

The following COPY statement will successfully load the table from the file and apply the DEFAULT value \('1000'\) to the omitted column: 

```
copy venue_new(venueid, venuename, venuecity, venuestate) 
from 's3://mybucket/data/venue_noseats.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|';
```

Now view the loaded table: 

```
select * from venue_new order by venueid;
venueid |         venuename          |    venuecity    | venuestate | venueseats
---------+----------------------------+-----------------+------------+------------
1 | Toyota Park                | Bridgeview      | IL         |       1000
2 | Columbus Crew Stadium      | Columbus        | OH         |       1000
3 | RFK Stadium                | Washington      | DC         |       1000
4 | CommunityAmerica Ballpark  | Kansas City     | KS         |       1000
5 | Gillette Stadium           | Foxborough      | MA         |       1000
6 | New York Giants Stadium    | East Rutherford | NJ         |       1000
7 | BMO Field                  | Toronto         | ON         |       1000
8 | The Home Depot Center      | Carson          | CA         |       1000
9 | Dick's Sporting Goods Park | Commerce City   | CO         |       1000
10 | Pizza Hut Park             | Frisco          | TX         |       1000
(10 rows)
```

For the following example, in addition to assuming that no VENUESEATS data is included in the file, also assume that no VENUENAME data is included: 

```
1||Bridgeview|IL|
2||Columbus|OH|
3||Washington|DC|
4||Kansas City|KS|
5||Foxborough|MA|
6||East Rutherford|NJ|
7||Toronto|ON|
8||Carson|CA|
9||Commerce City|CO|
10||Frisco|TX|
```

 Using the same table definition, the following COPY statement fails because no DEFAULT value was specified for VENUENAME, and VENUENAME is a NOT NULL column: 

```
copy venue(venueid, venuecity, venuestate) 
from 's3://mybucket/data/venue_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|';
```

Now consider a variation of the VENUE table that uses an IDENTITY column: 

```
create table venue_identity(
venueid int identity(1,1),
venuename varchar(100) not null,
venuecity varchar(30),
venuestate char(2),
venueseats integer not null default '1000');
```

As with the previous example, assume that the VENUESEATS column has no corresponding values in the source file\. The following COPY statement successfully loads the table, including the predefined IDENTITY data values instead of autogenerating those values: 

```
copy venue(venueid, venuename, venuecity, venuestate) 
from 's3://mybucket/data/venue_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|' explicit_ids;
```

This statement fails because it doesn't include the IDENTITY column \(VENUEID is missing from the column list\) yet includes an EXPLICIT\_IDS parameter: 

```
copy venue(venuename, venuecity, venuestate) 
from 's3://mybucket/data/venue_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|' explicit_ids;
```

This statement fails because it doesn't include an EXPLICIT\_IDS parameter: 

```
copy venue(venueid, venuename, venuecity, venuestate)
from 's3://mybucket/data/venue_pipe.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|';
```

## COPY data with the ESCAPE option<a name="r_COPY_command_examples-copy-data-with-the-escape-option"></a>

The following example shows how to load characters that match the delimiter character \(in this case, the pipe character\)\. In the input file, make sure that all of the pipe characters \(\|\) that you want to load are escaped with the backslash character \(\\\)\. Then load the file with the ESCAPE parameter\. 

```
$ more redshiftinfo.txt
1|public\|event\|dwuser
2|public\|sales\|dwuser

create table redshiftinfo(infoid int,tableinfo varchar(50));

copy redshiftinfo from 's3://mybucket/data/redshiftinfo.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
delimiter '|' escape;

select * from redshiftinfo order by 1;
infoid |       tableinfo
-------+--------------------
1      | public|event|dwuser
2      | public|sales|dwuser
(2 rows)
```

Without the ESCAPE parameter, this COPY command fails with an `Extra column(s) found` error\.

**Important**  
If you load your data using a COPY with the ESCAPE parameter, you must also specify the ESCAPE parameter with your UNLOAD command to generate the reciprocal output file\. Similarly, if you UNLOAD using the ESCAPE parameter, you need to use ESCAPE when you COPY the same data\.

## Copy from JSON examples<a name="r_COPY_command_examples-copy-from-json"></a>

In the following examples, you load the CATEGORY table with the following data\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_COPY_command_examples.html)

**Topics**
+ [Load from JSON data using the 'auto' option](#copy-from-json-examples-using-auto)
+ [Load from JSON data using the 'auto ignorecase' option](#copy-from-json-examples-using-auto-ignorecase)
+ [Load from JSON data using a JSONPaths file](#copy-from-json-examples-using-jsonpaths)
+ [Load from JSON arrays using a JSONPaths file](#copy-from-json-examples-using-jsonpaths-arrays)

### Load from JSON data using the 'auto' option<a name="copy-from-json-examples-using-auto"></a>

To load from JSON data using the `'auto'` option, the JSON data must consist of a set of objects\. The key names must match the column names, but the order doesn't matter\. The following shows the contents of a file named `category_object_auto.json`\.

```
{
    "catdesc": "Major League Baseball",
    "catid": 1,
    "catgroup": "Sports",
    "catname": "MLB"
}
{
    "catgroup": "Sports",
    "catid": 2,
    "catname": "NHL",
    "catdesc": "National Hockey League"
}{
    "catid": 3,
    "catname": "NFL",
    "catgroup": "Sports",
    "catdesc": "National Football League"
}
{
    "bogus": "Bogus Sports LLC",
    "catid": 4,
    "catgroup": "Sports",
    "catname": "NBA",
    "catdesc": "National Basketball Association"
}
{
    "catid": 5,
    "catgroup": "Shows",
    "catname": "Musicals",
    "catdesc": "All symphony, concerto, and choir concerts"
}
```

To load from the JSON data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_object_auto.json'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
json 'auto';
```

### Load from JSON data using the 'auto ignorecase' option<a name="copy-from-json-examples-using-auto-ignorecase"></a>

To load from JSON data using the `'auto ignorecase'` option, the JSON data must consist of a set of objects\. The case of the key names doesn't have to match the column names and the order doesn't matter\. The following shows the contents of a file named `category_object_auto-ignorecase.json`\.

```
{
    "CatDesc": "Major League Baseball",
    "CatID": 1,
    "CatGroup": "Sports",
    "CatName": "MLB"
}
{
    "CatGroup": "Sports",
    "CatID": 2,
    "CatName": "NHL",
    "CatDesc": "National Hockey League"
}{
    "CatID": 3,
    "CatName": "NFL",
    "CatGroup": "Sports",
    "CatDesc": "National Football League"
}
{
    "bogus": "Bogus Sports LLC",
    "CatID": 4,
    "CatGroup": "Sports",
    "CatName": "NBA",
    "CatDesc": "National Basketball Association"
}
{
    "CatID": 5,
    "CatGroup": "Shows",
    "CatName": "Musicals",
    "CatDesc": "All symphony, concerto, and choir concerts"
}
```

To load from the JSON data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_object_auto ignorecase.json'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
json 'auto ignorecase';
```

### Load from JSON data using a JSONPaths file<a name="copy-from-json-examples-using-jsonpaths"></a>

If the JSON data objects don't correspond directly to column names, you can use a JSONPaths file to map the JSON elements to columns\. The order doesn't matter in the JSON source data, but the order of the JSONPaths file expressions must match the column order\. Suppose that you have the following data file, named `category_object_paths.json`\.

```
{
    "one": 1,
    "two": "Sports",
    "three": "MLB",
    "four": "Major League Baseball"
}
{
    "three": "NHL",
    "four": "National Hockey League",
    "one": 2,
    "two": "Sports"
}
{
    "two": "Sports",
    "three": "NFL",
    "one": 3,
    "four": "National Football League"
}
{
    "one": 4,
    "two": "Sports",
    "three": "NBA",
    "four": "National Basketball Association"
}
{
    "one": 6,
    "two": "Shows",
    "three": "Musicals",
    "four": "All symphony, concerto, and choir concerts"
}
```

The following JSONPaths file, named `category_jsonpath.json`, maps the source data to the table columns\.

```
{
    "jsonpaths": [
        "$['one']",
        "$['two']",
        "$['three']",
        "$['four']"
    ]
}
```

To load from the JSON data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_object_paths.json'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
json 's3://mybucket/category_jsonpath.json';
```

### Load from JSON arrays using a JSONPaths file<a name="copy-from-json-examples-using-jsonpaths-arrays"></a>

To load from JSON data that consists of a set of arrays, you must use a JSONPaths file to map the array elements to columns\. Suppose that you have the following data file, named `category_array_data.json`\.

```
[1,"Sports","MLB","Major League Baseball"]
[2,"Sports","NHL","National Hockey League"]
[3,"Sports","NFL","National Football League"]
[4,"Sports","NBA","National Basketball Association"]
[5,"Concerts","Classical","All symphony, concerto, and choir concerts"]
```

The following JSONPaths file, named `category_array_jsonpath.json`, maps the source data to the table columns\.

```
{
    "jsonpaths": [
        "$[0]",
        "$[1]",
        "$[2]",
        "$[3]"
    ]
}
```

To load from the JSON data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_array_data.json'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
json 's3://mybucket/category_array_jsonpath.json';
```

## Copy from Avro examples<a name="r_COPY_command_examples-copy-from-avro"></a>

In the following examples, you load the CATEGORY table with the following data\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_COPY_command_examples.html)

**Topics**
+ [Load from Avro data using the 'auto' option](#copy-from-avro-examples-using-auto)
+ [Load from Avro data using the 'auto ignorecase' option](#copy-from-avro-examples-using-auto-ignorecase)
+ [Load from Avro data using a JSONPaths file](#copy-from-avro-examples-using-avropaths)

### Load from Avro data using the 'auto' option<a name="copy-from-avro-examples-using-auto"></a>

To load from Avro data using the `'auto'` argument, field names in the Avro schema must match the column names\. When using the `'auto'` argument, order doesn't matter\. The following shows the schema for a file named `category_auto.avro`\.

```
{
    "name": "category",
    "type": "record",
    "fields": [
        {"name": "catid", "type": "int"},
        {"name": "catdesc", "type": "string"},
        {"name": "catname", "type": "string"},
        {"name": "catgroup", "type": "string"},
}
```

The data in an Avro file is in binary format, so it isn't human\-readable\. The following shows a JSON representation of the data in the `category_auto.avro` file\. 

```
{
   "catid": 1,
   "catdesc": "Major League Baseball",
   "catname": "MLB",
   "catgroup": "Sports"
}
{
   "catid": 2,
   "catdesc": "National Hockey League",
   "catname": "NHL",
   "catgroup": "Sports"
}
{
   "catid": 3,
   "catdesc": "National Basketball Association",
   "catname": "NBA",
   "catgroup": "Sports"
}
{
   "catid": 4,
   "catdesc": "All symphony, concerto, and choir concerts",
   "catname": "Classical",
   "catgroup": "Concerts"
}
```

To load from the Avro data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_auto.avro'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
format as avro 'auto';
```

### Load from Avro data using the 'auto ignorecase' option<a name="copy-from-avro-examples-using-auto-ignorecase"></a>

To load from Avro data using the `'auto ignorecase'` argument, the case of the field names in the Avro schema does not have to match the case of column names\. When using the `'auto ignorecase'` argument, order doesn't matter\. The following shows the schema for a file named `category_auto-ignorecase.avro`\.

```
{
    "name": "category",
    "type": "record",
    "fields": [
        {"name": "CatID", "type": "int"},
        {"name": "CatDesc", "type": "string"},
        {"name": "CatName", "type": "string"},
        {"name": "CatGroup", "type": "string"},
}
```

The data in an Avro file is in binary format, so it isn't human\-readable\. The following shows a JSON representation of the data in the `category_auto-ignorecase.avro` file\. 

```
{
   "CatID": 1,
   "CatDesc": "Major League Baseball",
   "CatName": "MLB",
   "CatGroup": "Sports"
}
{
   "CatID": 2,
   "CatDesc": "National Hockey League",
   "CatName": "NHL",
   "CatGroup": "Sports"
}
{
   "CatID": 3,
   "CatDesc": "National Basketball Association",
   "CatName": "NBA",
   "CatGroup": "Sports"
}
{
   "CatID": 4,
   "CatDesc": "All symphony, concerto, and choir concerts",
   "CatName": "Classical",
   "CatGroup": "Concerts"
}
```

To load from the Avro data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_auto-ignorecase.avro'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
format as avro 'auto ignorecase';
```

### Load from Avro data using a JSONPaths file<a name="copy-from-avro-examples-using-avropaths"></a>

If the field names in the Avro schema don't correspond directly to column names, you can use a JSONPaths file to map the schema elements to columns\. The order of the JSONPaths file expressions must match the column order\. 

Suppose that you have a data file named `category_paths.avro` that contains the same data as in the previous example, but with the following schema\.

```
{
    "name": "category",
    "type": "record",
    "fields": [
        {"name": "id", "type": "int"},
        {"name": "desc", "type": "string"},
        {"name": "name", "type": "string"},
        {"name": "group", "type": "string"},
        {"name": "region", "type": "string"} 
     ]
}
```

The following JSONPaths file, named `category_path.avropath`, maps the source data to the table columns\.

```
{
    "jsonpaths": [
        "$['id']",
        "$['group']",
        "$['name']",
        "$['desc']"
    ]
}
```

To load from the Avro data file in the previous example, run the following COPY command\.

```
copy category
from 's3://mybucket/category_object_paths.avro'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
format avro 's3://mybucket/category_path.avropath ';
```

## Preparing files for COPY with the ESCAPE option<a name="r_COPY_preparing_data"></a>

The following example describes how you might prepare data to "escape" newline characters before importing the data into an Amazon Redshift table using the COPY command with the ESCAPE parameter\. Without preparing the data to delimit the newline characters, Amazon Redshift returns load errors when you run the COPY command, because the newline character is normally used as a record separator\. 

For example, consider a file or a column in an external table that you want to copy into an Amazon Redshift table\. If the file or column contains XML\-formatted content or similar data, you need to make sure that all of the newline characters \(\\n\) that are part of the content are escaped with the backslash character \(\\\)\. 

A file or table containing embedded newlines characters provides a relatively easy pattern to match\. Each embedded newline character most likely always follows a `>` character with potentially some white space characters \(`' '` or tab\) in between, as you can see in the following example of a text file named `nlTest1.txt`\. 

```
$ cat nlTest1.txt
<xml start>
<newline characters provide>
<line breaks at the end of each>
<line in content>
</xml>|1000
<xml>
</xml>|2000
```

With the following example, you can run a text\-processing utility to pre\-process the source file and insert escape characters where needed\. \(The `|` character is intended to be used as delimiter to separate column data when copied into an Amazon Redshift table\.\) 

```
$ sed -e ':a;N;$!ba;s/>[[:space:]]*\n/>\\\n/g' nlTest1.txt > nlTest2.txt
```

Similarly, you can use Perl to perform a similar operation: 

```
cat nlTest1.txt | perl -p -e 's/>\s*\n/>\\\n/g' > nlTest2.txt
```

To accommodate loading the data from the `nlTest2.txt` file into Amazon Redshift, we created a two\-column table in Amazon Redshift\. The first column c1, is a character column that holds XML\-formatted content from the `nlTest2.txt` file\. The second column c2 holds integer values loaded from the same file\. 

After running the `sed` command, you can correctly load data from the `nlTest2.txt` file into an Amazon Redshift table using the ESCAPE parameter\. 

**Note**  
When you include the ESCAPE parameter with the COPY command, it escapes a number of special characters that include the backslash character \(including newline\)\. 

```
copy t2 from 's3://mybucket/data/nlTest2.txt' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'  
escape
delimiter as '|';

select * from t2 order by 2;

c1           |  c2
-------------+------
<xml start>
<newline characters provide>
<line breaks at the end of each>
<line in content>
</xml>
| 1000
<xml>
</xml>       | 2000
(2 rows)
```

You can prepare data files exported from external databases in a similar way\. For example, with an Oracle database, you can use the REPLACE function on each affected column in a table that you want to copy into Amazon Redshift\. 

```
SELECT c1, REPLACE(c2, \n',\\n' ) as c2 from my_table_with_xml
```

In addition, many database export and extract, transform, load \(ETL\) tools that routinely process large amounts of data provide options to specify escape and delimiter characters\. 

## Loading a shapefile into Amazon Redshift<a name="copy-example-spatial-copy-shapefile"></a>

The following examples demonstrate how to load an Esri shapefile using COPY\. For more information about loading shapefiles, see [Loading a shapefile into Amazon Redshift](spatial-copy-shapefile.md)\. 

### Loading a shapefile<a name="copy-example-spatial-copy-shapefile-loading-copy"></a>

The following steps show how to ingest OpenStreetMap data from Amazon S3 using the COPY command\. This example assumes that the Norway shapefile archive from [the download site of Geofabrik](https://download.geofabrik.de/europe.html) has been uploaded to a private Amazon S3 bucket in your AWS Region\. The `.shp`, `.shx`, and `.dbf` files must share the same Amazon S3 prefix and file name\.

#### Ingesting data without simplification<a name="spatial-copy-shapefile-loading-copy-fits"></a>

The following commands create tables and ingest data that can fit in the maximum geometry size without any simplification\. Open the `gis_osm_natural_free_1.shp` in your preferred GIS software and inspect the columns in this layer\.  By default, either IDENTITY or GEOMETRY columns are first\. When a GEOMETRY column is first, you can create the table as shown following\.

```
CREATE TABLE norway_natural (
   wkb_geometry GEOMETRY,
   osm_id BIGINT,
   code INT,
   fclass VARCHAR,
   name VARCHAR);
```

Or, when an IDENTITY column is first, you can create the table as shown following\.

```
CREATE TABLE norway_natural_with_id (
   fid INT IDENTITY(1,1),
   wkb_geometry GEOMETRY,
   osm_id BIGINT,
   code INT,
   fclass VARCHAR,
   name VARCHAR);
```

Now you can ingest the data using COPY\.

```
COPY norway_natural FROM 's3://bucket_name/shapefiles/norway/gis_osm_natural_free_1.shp'
FORMAT SHAPEFILE
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';
INFO: Load into table 'norway_natural' completed, 83891 record(s) loaded successfully
```

Or you can ingest the data as shown following\. 

```
COPY norway_natural_with_id FROM 's3://bucket_name/shapefiles/norway/gis_osm_natural_free_1.shp'
FORMAT SHAPEFILE
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';
INFO: Load into table 'norway_natural_with_id' completed, 83891 record(s) loaded successfully.
```

#### Ingesting data with simplification<a name="spatial-copy-shapefile-loading-copy-no-fit"></a>

The following commands create a table and try to ingest data that can't fit in the maximum geometry size without any simplification\. Inspect the `gis_osm_water_a_free_1.shp` shapefile and create the appropriate table as shown following\.

```
CREATE TABLE norway_water (
   wkb_geometry GEOMETRY,
   osm_id BIGINT,
   code INT,
   fclass VARCHAR,
   name VARCHAR);
```

When the COPY command runs, it results in an error\.

```
COPY norway_water FROM 's3://bucket_name/shapefiles/norway/gis_osm_water_a_free_1.shp'
FORMAT SHAPEFILE
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';
ERROR:  Load into table 'norway_water' failed.  Check 'stl_load_errors' system table for details.
```

Querying `STL_LOAD_ERRORS` shows that the geometry is too large\. 

```
SELECT line_number, btrim(colname), btrim(err_reason) FROM stl_load_errors WHERE query = pg_last_copy_id();
 line_number |    btrim     |                                 btrim
-------------+--------------+-----------------------------------------------------------------------
     1184705 | wkb_geometry | Geometry size: 1513736 is larger than maximum supported size: 1048447
```

To overcome this, the `SIMPLIFY AUTO` parameter is added to the COPY command to simplify geometries\.

```
COPY norway_water FROM 's3://bucket_name/shapefiles/norway/gis_osm_water_a_free_1.shp'
FORMAT SHAPEFILE
SIMPLIFY AUTO
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';

INFO:  Load into table 'norway_water' completed, 1989196 record(s) loaded successfully.
```

To view the rows and geometries that were simplified, query `SVL_SPATIAL_SIMPLIFY`\.

```
SELECT * FROM svl_spatial_simplify WHERE query = pg_last_copy_id();
 query | line_number | maximum_tolerance | initial_size | simplified | final_size |   final_tolerance
-------+-------------+-------------------+--------------+------------+------------+----------------------
    20 |     1184704 |                -1 |      1513736 | t          |    1008808 |   1.276386653895e-05
    20 |     1664115 |                -1 |      1233456 | t          |    1023584 | 6.11707814796635e-06
```

Using SIMPLIFY AUTO *max\_tolerance* with the tolerance lower than the automatically calculated ones probably results in an ingestion error\. In this case, use MAXERROR to ignore errors\.

```
COPY norway_water FROM 's3://bucket_name/shapefiles/norway/gis_osm_water_a_free_1.shp'
FORMAT SHAPEFILE
SIMPLIFY AUTO 1.1E-05
MAXERROR 2
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';

INFO:  Load into table 'norway_water' completed, 1989195 record(s) loaded successfully.
INFO:  Load into table 'norway_water' completed, 1 record(s) could not be loaded.  Check 'stl_load_errors' system table for details.
```

Query `SVL_SPATIAL_SIMPLIFY` again to identify the record that COPY didn't manage to load\.

```
SELECT * FROM svl_spatial_simplify WHERE query = pg_last_copy_id();
 query | line_number | maximum_tolerance | initial_size | simplified | final_size | final_tolerance
-------+-------------+-------------------+--------------+------------+------------+-----------------
    29 |     1184704 |           1.1e-05 |      1513736 | f          |          0 |               0
    29 |     1664115 |           1.1e-05 |      1233456 | t          |     794432 |         1.1e-05
```

In this example, the first record didn’t manage to fit, so the `simplified` column is showing false\. The second record was loaded within the given tolerance\. However, the final size is larger than using the automatically calculated tolerance without specifying the maximum tolerance\. 

### Loading from a compressed shapefile<a name="copy-example-spatial-copy-shapefile-compressed"></a>

Amazon Redshift COPY supports ingesting data from a compressed shapefile\. All shapefile components must have the same Amazon S3 prefix and the same compression suffix\. As an example, suppose that you want to load the data from the previous example\. In this case, the files `gis_osm_water_a_free_1.shp.gz`, `gis_osm_water_a_free_1.dbf.gz`, and `gis_osm_water_a_free_1.shx.gz` must share the same Amazon S3 directory\. The COPY command requires the GZIP option, and the FROM clause must specify the correct compressed file, as shown following\.

```
COPY norway_natural FROM 's3://bucket_name/shapefiles/norway/compressed/gis_osm_natural_free_1.shp.gz'
FORMAT SHAPEFILE
GZIP
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';
INFO:  Load into table 'norway_natural' completed, 83891 record(s) loaded successfully.
```

### Loading data into a table with a different column order<a name="copy-example-spatial-copy-shapefile-column-order"></a>

If you have a table that doesn't have `GEOMETRY` as the first column, you can use column mapping to map columns to the target table\. For example, create a table with `osm_id` specified as a first column\.

```
CREATE TABLE norway_natural_order (
   osm_id BIGINT,
   wkb_geometry GEOMETRY,
   code INT,
   fclass VARCHAR,
   name VARCHAR);
```

Then ingest a shapefile using column mapping\.

```
COPY norway_natural_order(wkb_geometry, osm_id, code, fclass, name) 
FROM 's3://bucket_name/shapefiles/norway/gis_osm_natural_free_1.shp'
FORMAT SHAPEFILE
CREDENTIALS 'aws_iam_role=arn:aws:iam::123456789012:role/MyRoleName';
INFO:  Load into table 'norway_natural_order' completed, 83891 record(s) loaded successfully.
```