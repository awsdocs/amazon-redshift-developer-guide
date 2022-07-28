# STL\_DDLTEXT<a name="r_STL_DDLTEXT"></a>

Captures the following DDL statements that were run on the system\.

These DDL statements include the following queries and objects: 
+ CREATE SCHEMA, TABLE, VIEW
+ DROP SCHEMA, TABLE, VIEW
+ ALTER SCHEMA, TABLE

See also [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\. These views provide a timeline of the SQL commands that are run on the system; this history is useful for troubleshooting and for creating an audit trail of all system activities\.

Use the STARTTIME and ENDTIME columns to find out which statements were logged during a given time period\. Long blocks of SQL text are broken into lines 200 characters long; the SEQUENCE column identifies fragments of text that belong to a single statement\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_DDLTEXT-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DDLTEXT.html)

## Sample queries<a name="r_STL_DDLTEXT-sample-queries2"></a>

The following query returns records that include previously run DDL statements\.

```
select xid, starttime, sequence, substring(text,1,40) as text
from stl_ddltext order by xid desc, sequence;
```

The following is sample output that shows four CREATE TABLE statements\. The DDL statements appear in the `text` column, which is truncated for readability\.

```
 xid  |         starttime          | sequence |                   text
------+----------------------------+----------+------------------------------------------
 1806 | 2013-10-23 00:11:14.709851 |        0 | CREATE TABLE supplier ( s_suppkey int4 N
 1806 | 2013-10-23 00:11:14.709851 |        1 |  s_comment varchar(101) NOT NULL )
 1805 | 2013-10-23 00:11:14.496153 |        0 | CREATE TABLE region ( r_regionkey int4 N
 1804 | 2013-10-23 00:11:14.285986 |        0 | CREATE TABLE partsupp ( ps_partkey int8
 1803 | 2013-10-23 00:11:14.056901 |        0 | CREATE TABLE part ( p_partkey int8 NOT N
 1803 | 2013-10-23 00:11:14.056901 |        1 | ner char(10) NOT NULL , p_retailprice nu
(6 rows)
```

### Reconstructing Stored SQL<a name="r_STL_DDLTEXT-reconstruct-sql"></a>

The following SQL lists rows stored in the `text` column of STL\_DDLTEXT\. The rows are ordered by `xid` and `sequence`\. If the original SQL was longer than 200 characters multiple rows, STL\_DDLTEXT can contain multiple rows by `sequence`\. 

```
SELECT xid, sequence, LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END, '') WITHIN GROUP (ORDER BY sequence) as query_statement 
FROM stl_ddltext GROUP BY xid, sequence ORDER BY xid, sequence;
```

```
xid     |  sequence | query_statement
--------+-----------+-----------------
7886671    0          create external schema schema_spectrum_uddh\nfrom data catalog\ndatabase 'spectrum_db_uddh'\niam_role ''\ncreate external database if not exists;	
7886752    0          CREATE EXTERNAL TABLE schema_spectrum_uddh.soccer_league\n(\n  league_rank smallint,\n  prev_rank   smallint,\n  club_name   varchar(15),\n  league_name varchar(20),\n  league_off  decimal(6,2),\n  le	
7886752    1          ague_def  decimal(6,2),\n  league_spi  decimal(6,2),\n  league_nspi smallint\n)\nROW FORMAT DELIMITED \n    FIELDS TERMINATED BY ',' \n    LINES TERMINATED BY '\\n\\l'\nstored as textfile\nLOCATION 's	
7886752    2          3://mybucket-spectrum-uddh/'\ntable properties ('skip.header.line.count'='1');
...
```

To reconstruct the SQL stored in the `text` column of STL\_DDLTEXT, run the following SQL statement\. It puts together DDL statements from one or more segments in the `text` column\. Before running the reconstructed SQL, replace any \(`\n`\) special characters with a new line in your SQL client\. The results of the following SELECT statement puts together three rows in sequence order to reconstruct the SQL, in the `query_statement` field\. 

```
SELECT LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END) WITHIN GROUP (ORDER BY sequence) as query_statement
FROM stl_ddltext GROUP BY xid, endtime order by xid, endtime;
```

```
query_statement
--------------
create external schema schema_spectrum_uddh\nfrom data catalog\ndatabase 'spectrum_db_uddh'\niam_role ''\ncreate external database if not exists;	
CREATE EXTERNAL TABLE schema_spectrum_uddh.soccer_league\n(\n  league_rank smallint,\n  prev_rank   smallint,\n  club_name   varchar(15),\n  league_name varchar(20),\n  league_off  decimal(6,2),\n  league_def  decimal(6,2),\n  league_spi  decimal(6,2),\n  league_nspi smallint\n)\nROW FORMAT DELIMITED \n    FIELDS TERMINATED BY ',' \n    LINES TERMINATED BY '\\n\\l'\nstored as textfile\nLOCATION 's3://mybucket-spectrum-uddh/'\ntable properties ('skip.header.line.count'='1');
```