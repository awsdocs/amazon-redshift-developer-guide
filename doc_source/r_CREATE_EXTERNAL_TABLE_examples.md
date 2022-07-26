# Examples<a name="r_CREATE_EXTERNAL_TABLE_examples"></a>

The following example creates a table named SALES in the Amazon Redshift external schema named `spectrum`\. The data is in tab\-delimited text files\. The TABLE PROPERTIES clause sets the numRows property to 170,000 rows\.

```
create external table spectrum.sales(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
saledate date,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
row format delimited
fields terminated by '\t'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales/'
table properties ('numRows'='170000');
```

The following example creates a table that uses the JsonSerDe to reference data in JSON format\.

```
create external table spectrum.cloudtrail_json (
event_version int,
event_id bigint,
event_time timestamp,
event_type varchar(10),
awsregion varchar(20),
event_name varchar(max),
event_source varchar(max),
requesttime timestamp,
useragent varchar(max),
recipientaccountid bigint) 
row format serde 'org.openx.data.jsonserde.JsonSerDe'
with serdeproperties (
'dots.in.keys' = 'true',
'mapping.requesttime' = 'requesttimestamp'
) location 's3://mybucket/json/cloudtrail';
```

The following CREATE EXTERNAL TABLE AS example creates a nonpartitioned external table\. Then it writes the result of the SELECT query as Apache Parquet to the target Amazon S3 location\.

```
CREATE EXTERNAL TABLE spectrum.lineitem
STORED AS parquet
LOCATION 'S3://mybucket/cetas/lineitem/'
AS SELECT * FROM local_lineitem;
```

The following example creates a partitioned external table and includes the partition columns in the SELECT query\. 

```
CREATE EXTERNAL TABLE spectrum.partitioned_lineitem
PARTITIONED BY (l_shipdate, l_shipmode)
STORED AS parquet
LOCATION 'S3://mybucket/cetas/partitioned_lineitem/'
AS SELECT l_orderkey, l_shipmode, l_shipdate, l_partkey FROM local_table;
```

For a list of existing databases in the external data catalog, query the [SVV\_EXTERNAL\_DATABASES](r_SVV_EXTERNAL_DATABASES.md) system view\. 

```
select eskind,databasename,esoptions from svv_external_databases order by databasename;
```

```
eskind | databasename | esoptions                                                                        
-------+--------------+----------------------------------------------------------------------------------
     1 | default      | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
     1 | sampledb     | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
     1 | spectrumdb   | {"REGION":"us-west-2","IAM_ROLE":"arn:aws:iam::123456789012:role/mySpectrumRole"}
```

To view details of external tables, query the [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) system views\.

The following example queries the SVV\_EXTERNAL\_TABLES view\.

```
select schemaname, tablename, location from svv_external_tables;
```

```
schemaname | tablename            | location                                               
-----------+----------------------+--------------------------------------------------------
spectrum   | sales                | s3://awssampledbuswest2/tickit/spectrum/sales          
spectrum   | sales_part           | s3://awssampledbuswest2/tickit/spectrum/sales_partition
```

The following example queries the SVV\_EXTERNAL\_COLUMNS view\. 

```
select * from svv_external_columns where schemaname like 'spectrum%' and tablename ='sales';
```

```
schemaname | tablename | columnname | external_type | columnnum | part_key
-----------+-----------+------------+---------------+-----------+---------
spectrum   | sales     | salesid    | int           |         1 |        0
spectrum   | sales     | listid     | int           |         2 |        0
spectrum   | sales     | sellerid   | int           |         3 |        0
spectrum   | sales     | buyerid    | int           |         4 |        0
spectrum   | sales     | eventid    | int           |         5 |        0
spectrum   | sales     | saledate   | date          |         6 |        0
spectrum   | sales     | qtysold    | smallint      |         7 |        0
spectrum   | sales     | pricepaid  | decimal(8,2)  |         8 |        0
spectrum   | sales     | commission | decimal(8,2)  |         9 |        0
spectrum   | sales     | saletime   | timestamp     |        10 |        0
```

To view table partitions, use the following query\.

```
select schemaname, tablename, values, location 
from svv_external_partitions
where tablename = 'sales_part';
```

```
schemaname | tablename  | values         | location                                                                
-----------+------------+----------------+-------------------------------------------------------------------------
spectrum   | sales_part | ["2008-01-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01
spectrum   | sales_part | ["2008-02-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02
spectrum   | sales_part | ["2008-03-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03
spectrum   | sales_part | ["2008-04-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04
spectrum   | sales_part | ["2008-05-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05
spectrum   | sales_part | ["2008-06-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06
spectrum   | sales_part | ["2008-07-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07
spectrum   | sales_part | ["2008-08-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08
spectrum   | sales_part | ["2008-09-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09
spectrum   | sales_part | ["2008-10-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10
spectrum   | sales_part | ["2008-11-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11
spectrum   | sales_part | ["2008-12-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12
```

The following example returns the total size of related data files for an external table\.

```
select distinct "$path", "$size"
   from spectrum.sales_part;

 $path                                 | $size
---------------------------------------+-------
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/ |  1616
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/ |  1444
```

## Partitioning examples<a name="r_CREATE_EXTERNAL_TABLE_examples-partitioning"></a>

To create an external table partitioned by date, run the following command\.

```
create external table spectrum.sales_part(
salesid integer,
listid integer,
sellerid integer,
buyerid integer,
eventid integer,
dateid smallint,
qtysold smallint,
pricepaid decimal(8,2),
commission decimal(8,2),
saletime timestamp)
partitioned by (saledate date)
row format delimited
fields terminated by '|'
stored as textfile
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/'
table properties ('numRows'='170000');
```

To add the partitions, run the following ALTER TABLE commands\.

```
alter table spectrum.sales_part
add if not exists partition (saledate='2008-01-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-02-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-03-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-04-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-05-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-06-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-07-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-08-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-09-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-10-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-11-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11/';
alter table spectrum.sales_part
add if not exists partition (saledate='2008-12-01') 
location 's3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12/';
```

To select data from the partitioned table, run the following query\.

```
select top 10 spectrum.sales_part.eventid, sum(spectrum.sales_part.pricepaid) 
from spectrum.sales_part, event
where spectrum.sales_part.eventid = event.eventid
  and spectrum.sales_part.pricepaid > 30
  and saledate = '2008-12-01'
group by spectrum.sales_part.eventid
order by 2 desc;
```

```
eventid | sum     
--------+---------
    914 | 36173.00
   5478 | 27303.00
   5061 | 26383.00
   4406 | 26252.00
   5324 | 24015.00
   1829 | 23911.00
   3601 | 23616.00
   3665 | 23214.00
   6069 | 22869.00
   5638 | 22551.00
```

To view external table partitions, query the [SVV\_EXTERNAL\_PARTITIONS](r_SVV_EXTERNAL_PARTITIONS.md) system view\.

```
select schemaname, tablename, values, location from svv_external_partitions
where tablename = 'sales_part';
```

```
schemaname | tablename  | values         | location                                         
-----------+------------+----------------+--------------------------------------------------
spectrum   | sales_part | ["2008-01-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-01
spectrum   | sales_part | ["2008-02-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-02
spectrum   | sales_part | ["2008-03-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-03
spectrum   | sales_part | ["2008-04-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-04
spectrum   | sales_part | ["2008-05-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-05
spectrum   | sales_part | ["2008-06-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-06
spectrum   | sales_part | ["2008-07-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-07
spectrum   | sales_part | ["2008-08-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-08
spectrum   | sales_part | ["2008-09-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-09
spectrum   | sales_part | ["2008-10-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-10
spectrum   | sales_part | ["2008-11-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-11
spectrum   | sales_part | ["2008-12-01"] | s3://awssampledbuswest2/tickit/spectrum/sales_partition/saledate=2008-12
```

## Row format examples<a name="r_CREATE_EXTERNAL_TABLE_examples-row-format"></a>

The following shows an example of specifying the ROW FORMAT SERDE parameters for data files stored in AVRO format\.

```
create external table spectrum.sales(salesid int, listid int, sellerid int, buyerid int, eventid int, dateid int, qtysold int, pricepaid decimal(8,2), comment VARCHAR(255))
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
WITH SERDEPROPERTIES ('avro.schema.literal'='{\"namespace\": \"dory.sample\",\"name\": \"dory_avro\",\"type\": \"record\", \"fields\": [{\"name\":\"salesid\", \"type\":\"int\"}, 
{\"name\":\"listid\", \"type\":\"int\"}, 
{\"name\":\"sellerid\", \"type\":\"int\"}, 
{\"name\":\"buyerid\", \"type\":\"int\"}, 
{\"name\":\"eventid\",\"type\":\"int\"}, 
{\"name\":\"dateid\",\"type\":\"int\"}, 
{\"name\":\"qtysold\",\"type\":\"int\"}, 
{\"name\":\"pricepaid\", \"type\": {\"type\": \"bytes\", \"logicalType\": \"decimal\", \"precision\": 8, \"scale\": 2}}, {\"name\":\"comment\",\"type\":\"string\"}]}')
STORED AS AVRO
location 's3://mybucket/avro/sales' ;
```

The following shows an example of specifying the ROW FORMAT SERDE parameters using RegEx\.

```
create external table spectrum.types(
cbigint bigint,
cbigint_null bigint,
cint int,
cint_null int)
row format serde 'org.apache.hadoop.hive.serde2.RegexSerDe'
with serdeproperties ('input.regex'='([^\\x01]+)\\x01([^\\x01]+)\\x01([^\\x01]+)\\x01([^\\x01]+)')
stored as textfile
location 's3://mybucket/regex/types';
```

The following shows an example of specifying the ROW FORMAT SERDE parameters using Grok\.

```
create external table spectrum.grok_log(
timestamp varchar(255),
pid varchar(255),
loglevel varchar(255),
progname varchar(255),
message varchar(255))
row format serde 'com.amazonaws.glue.serde.GrokSerDe'
with serdeproperties ('input.format'='[DFEWI], \\[%{TIMESTAMP_ISO8601:timestamp} #%{POSINT:pid:int}\\] *(?<loglevel>:DEBUG|FATAL|ERROR|WARN|INFO) -- +%{DATA:progname}: %{GREEDYDATA:message}')
stored as textfile
location 's3://mybucket/grok/logs';
```

The following shows an example of defining an Amazon S3 server access log in an S3 bucket\. You can use Redshift Spectrum to query Amazon S3 access logs\.

```
CREATE EXTERNAL TABLE spectrum.mybucket_s3_logs(
bucketowner varchar(255),
bucket varchar(255),
requestdatetime varchar(2000),
remoteip varchar(255),
requester varchar(255),
requested varchar(255),
operation varchar(255),
key varchar(255),
requesturi_operation varchar(255),
requesturi_key varchar(255),
requesturi_httpprotoversion varchar(255),
httpstatus varchar(255),
errorcode varchar(255),
bytessent bigint,
objectsize bigint,
totaltime varchar(255),
turnaroundtime varchar(255),
referrer varchar(255),
useragent varchar(255),
versionid varchar(255)
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
'input.regex' = '([^ ]*) ([^ ]*) \\[(.*?)\\] ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) \"([^ ]*)\\s*([^ ]*)\\s*([^ ]*)\" (- |[^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) (\"[^\"]*\") ([^ ]*).*$')
LOCATION 's3://mybucket/s3logs’;
```

The following shows an example of specifying the ROW FORMAT SERDE parameters for ION format data\.

```
CREATE EXTERNAL TABLE tbl_name (columns)
ROW FORMAT SERDE 'com.amazon.ionhiveserde.IonHiveSerDe'
STORED AS
INPUTFORMAT 'com.amazon.ionhiveserde.formats.IonInputFormat'
OUTPUTFORMAT 'com.amazon.ionhiveserde.formats.IonOutputFormat'
LOCATION 's3://s3-bucket/prefix'
```

## Data handling examples<a name="r_CREATE_EXTERNAL_TABLE_examples-data-handling"></a>

The following examples access the file: [spi\_global\_rankings\.csv](https://s3.amazonaws.com/redshift-downloads/docs-downloads/spi_global_rankings.csv)\. You can upload the `spi_global_rankings.csv` file to an Amazon S3 bucket to try these examples\.

The following example creates the external schema `schema_spectrum_uddh` and database `spectrum_db_uddh`\. For `aws-account-id`, enter your AWS account ID and for `role-name` enter your Redshift Spectrum role name\.

```
create external schema schema_spectrum_uddh
from data catalog
database 'spectrum_db_uddh'
iam_role 'arn:aws:iam::aws-account-id:role/role-name'
create external database if not exists;
```

The following example creates the external table `soccer_league` in the external schema `schema_spectrum_uddh`\.

```
CREATE EXTERNAL TABLE schema_spectrum_uddh.soccer_league
(
  league_rank smallint,
  prev_rank   smallint,
  club_name   varchar(15),
  league_name varchar(20),
  league_off  decimal(6,2),
  league_def  decimal(6,2),
  league_spi  decimal(6,2),
  league_nspi smallint
)
ROW FORMAT DELIMITED 
    FIELDS TERMINATED BY ',' 
    LINES TERMINATED BY '\n\l'
stored as textfile
LOCATION 's3://spectrum-uddh/league/'
table properties ('skip.header.line.count'='1');
```

Check the number of rows in the `soccer_league` table\.

```
select count(*) from schema_spectrum_uddh.soccer_league;
```

The numbers of rows displays\.

```
count
645
```

The following query displays the top 10 clubs\.

```
select league_rank,club_name,league_name,league_nspi
from schema_spectrum_uddh.soccer_league
where league_rank between 1 and 10;
```

```
league_rank	club_name	league_name			league_nspi
1		Manchester City	Barclays Premier Lea		32767
2		Bayern Munich	German Bundesliga		32767
3		Liverpool	Barclays Premier Lea		32767
4		Chelsea		Barclays Premier Lea		32767
5		Ajax		Dutch Eredivisie		32767
6		Atletico 	Madrid	Spanish Primera Divi	31517
7		Real Madrid	Spanish Primera Divi		31469
8		NULL	Spanish Primera Divi			31321
9		RB Leipzig	German Bundesliga		31014
10		Paris Saint-Ger	French Ligue 1			30929
```

The following example alters the `soccer_league` table to specify the `invalid_char_handling`, `replacement_char`, and `data_cleansing_enabled` external table parameters to insert a question mark \(?\) as a substitute for unexpected characters\.

```
alter  table schema_spectrum_uddh.soccer_league
set table properties ('invalid_char_handling'='REPLACE','replacement_char'='?','data_cleansing_enabled'='true');
```

The following example queries the table `soccer_league` for teams with a rank from 1 to 10\.

```
select league_rank,club_name,league_name,league_nspi
from schema_spectrum_uddh.soccer_league
where league_rank between 1 and 10;
```

The following query displays the top 10 clubs, with the question mark \(?\) replacement character in the eighth row\.

```
league_rank	club_name	league_name		league_nspi
1		Manchester City	Barclays Premier Lea	32767
2		Bayern Munich	German Bundesliga	32767
3		Liverpool	Barclays Premier Lea	32767
4		Chelsea		Barclays Premier Lea	32767
5		Ajax		Dutch Eredivisie	32767
6		Atletico Madrid	Spanish Primera Divi	31517
7		Real Madrid	Spanish Primera Divi	31469
8		Barcel?na	Spanish Primera Divi	31321
9		RB Leipzig	German Bundesliga	31014
10		Paris Saint-Ger	French Ligue 1		30929
```

The following example alters the `soccer_league` table to specify the `invalid_char_handling` external table parameters to drop rows with unexpected characters\.

```
alter table schema_spectrum_uddh.soccer_league 
set table properties ('invalid_char_handling'='DROP_ROW','data_cleansing_enabled'='true');
```

The following example queries the table `soccer_league` for teams with a rank from 1 to 10\.

```
select league_rank,club_name,league_name,league_nspi
from schema_spectrum_uddh.soccer_league
where league_rank between 1 and 10;
```

The following query displays the top 10 clubs, not including the eighth row\.

```
league_rank	club_name	league_name	league_nspi
1	 Manchester City	Barclays Premier Lea	32767
2	 Bayern Munich	German Bundesliga	32767
3	 Liverpool	Barclays Premier Lea	32767
4	 Chelsea   	Barclays Premier Lea	32767
5	 Ajax   	Dutch Eredivisie	32767
6	 Atletico Madrid	Spanish Primera Divi	31517
7	 Real Madrid	Spanish Primera Divi	31469
9	 RB Leipzig	German Bundesliga	31014
10	   Paris Saint-Ger	French Ligue 1	30929
```