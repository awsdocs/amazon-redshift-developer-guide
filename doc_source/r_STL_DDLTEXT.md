# STL\_DDLTEXT<a name="r_STL_DDLTEXT"></a>

Captures the following DDL statements that were run on the system\.

These DDL statements include the following queries and objects: 
+ CREATE SCHEMA, TABLE, VIEW
+ DROP SCHEMA, TABLE, VIEW
+ ALTER SCHEMA, TABLE

See also [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\. These views provide a timeline of the SQL commands that are executed on the system; this history is useful for troubleshooting purposes and for creating an audit trail of all system activities\.

Use the STARTTIME and ENDTIME columns to find out which statements were logged during a given time period\. Long blocks of SQL text are broken into lines 200 characters long; the SEQUENCE column identifies fragments of text that belong to a single statement\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_DDLTEXT-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DDLTEXT.html)

## Sample queries<a name="r_STL_DDLTEXT-sample-queries2"></a>

The following query shows the DDL for four CREATE TABLE statements\. The DDL text column is truncated for readability\.

```
select xid, starttime, sequence, substring(text,1,40) as text
from stl_ddltext order by xid desc, sequence;

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

To reconstruct the SQL stored in the `text` column of STL\_DDLTEXT, run a SELECT statement to create SQL from 1 or more parts in the `text` column\. Before running the reconstructed SQL, replace any \(`\n`\) special characters with a new line\. The result of the following SELECT statement is rows of reconstructed SQL in the `query_statement` field\.

```
SELECT query, LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END) WITHIN GROUP (ORDER BY sequence) as query_statement, COUNT(*) as row_count 
FROM stl_ddltext GROUP BY query ORDER BY query desc;
```

For example, the following query runs several DDL statements\. The query itself is longer than 200 characters and is stored in several parts in STL\_DDLTEXT\. 

```
DROP TABLE IF EXISTS public.t_tx_trunc;
CREATE TABLE public.t_tx_trunc(a varchar);
CREATE OR REPLACE PROCEDURE public.sp_truncate_top_level()
LANGUAGE plpgsql
AS $$
DECLARE
  row_cnt int;
BEGIN
  INSERT INTO public.t_tx_trunc VALUES ('Insert in SP: Before Truncate    0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000');
  select count(*) into row_cnt from public.t_tx_trunc;
  RAISE INFO 'sp_truncate_top_level: RowCount after 1st Insert: %', row_cnt;
  truncate table public.t_tx_trunc;
  select count(*) into row_cnt from public.t_tx_trunc;
  RAISE INFO 'sp_truncate_top_level: RowCount After Truncate: %', row_cnt;
  INSERT INTO public.t_tx_trunc VALUES ('Insert 1 in SP: After Truncate');
  select count(*) into row_cnt from public.t_tx_trunc;
  RAISE INFO 'sp_truncate_top_level: RowCount after 2nd Insert: %', row_cnt;
  INSERT INTO public.t_tx_trunc VALUES ('Insert 2 in SP: After Truncate');
  select count(*) into row_cnt from public.t_tx_trunc;
  RAISE INFO 'sp_truncate_top_level: RowCount after 3rd Insert: %', row_cnt;
END
$$;
DROP PROCEDURE sp_truncate_top_level();
DROP TABLE IF EXISTS public.t_tx_trunc;
```

In this example, the query is stored in many parts \(rows\) in the `text` column of STL\_DDLTEXT\.

```
select starttime, sequence, text
from stl_ddltext where query=pg_last_query_id() order by starttime, sequence limit 10;
```

```
         starttime          | sequence |                                                                                                   text                  
                                                                                 
----------------------------+----------+-------------------------------------------------------------------------------------------------------------------------
 2019-07-23 23:08:15.672457 |        0 | DROP TABLE IF EXISTS public.t_tx_trunc;
 2019-07-23 23:08:15.676281 |        0 | CREATE TABLE public.t_tx_trunc(a varchar);
 2019-07-23 23:08:15.727303 |        0 | CREATE OR REPLACE PROCEDURE public.sp_truncate_top_level()\nLANGUAGE plpgsql\nAS $$\nDECLARE\n  row_cnt int;\nBEGIN\n  INSERT INTO public.t_tx_trunc VALUES ('Insert in SP: Before Truncate    000000000
 2019-07-23 23:08:15.727303 |        1 | 0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000');\n  select count(*) into row_cnt from public.t
 2019-07-23 23:08:15.727303 |        2 | _tx_trunc;\n  RAISE INFO 'sp_truncate_top_level: RowCount after 1st Insert: %', row_cnt;\n  truncate table public.t_tx_trunc;\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE INFO 'sp_
 2019-07-23 23:08:15.727303 |        3 | truncate_top_level: RowCount After Truncate: %', row_cnt;\n  INSERT INTO public.t_tx_trunc VALUES ('Insert 1 in SP: After Truncate');\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE I
 2019-07-23 23:08:15.727303 |        4 | NFO 'sp_truncate_top_level: RowCount after 2nd Insert: %', row_cnt;\n  INSERT INTO public.t_tx_trunc VALUES ('Insert 2 in SP: After Truncate');\n  select count(*) into row_cnt from public.t_tx_trunc;
 2019-07-23 23:08:15.727303 |        5 | \n  RAISE INFO 'sp_truncate_top_level: RowCount after 3rd Insert: %', row_cnt;\nEND\n$$;
 2019-07-23 23:08:15.76039  |        0 | DROP PROCEDURE sp_truncate_top_level();
 2019-07-23 23:08:16.454956 |        0 | DROP TABLE IF EXISTS public.t_tx_trunc;
```

To reconstruct the SQL stored in STL\_DDLTEXT, run the following SQL\. 

```
SELECT LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END) WITHIN GROUP (ORDER BY sequence) as query_statement 
FROM stl_ddltext GROUP BY xid order by xid;
```

To use the resulting reconstructed SQL in your client, replace any \(`\n`\) special characters with a new line\. 

```
                                                      query_statement                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 DROP TABLE IF EXISTS public.t_tx_trunc;
 CREATE TABLE public.t_tx_trunc(a varchar);
 CREATE OR REPLACE PROCEDURE public.sp_truncate_top_level()\nLANGUAGE plpgsql\nAS $$\nDECLARE\n  row_cnt int;\nBEGIN\n  INSERT INTO public.t_tx_trunc VALUES ('Insert in SP: Before Truncate    0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000');\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE INFO 'sp_truncate_top_level: RowCount after 1st Insert: %', row_cnt;\n  truncate table public.t_tx_trunc;\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE INFO 'sp_truncate_top_level: RowCount After Truncate: %', row_cnt;\n  INSERT INTO public.t_tx_trunc VALUES ('Insert 1 in SP: After Truncate');\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE INFO 'sp_truncate_top_level: RowCount after 2nd Insert: %', row_cnt;\n  INSERT INTO public.t_tx_trunc VALUES ('Insert 2 in SP: After Truncate');\n  select count(*) into row_cnt from public.t_tx_trunc;\n  RAISE INFO 'sp_truncate_top_level: RowCount after 3rd Insert: %', row_cnt;\nEND\n$$;
 DROP PROCEDURE sp_truncate_top_level();
 DROP TABLE IF EXISTS public.t_tx_trunc;
```