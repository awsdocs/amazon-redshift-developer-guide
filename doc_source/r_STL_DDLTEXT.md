# STL\_DDLTEXT<a name="r_STL_DDLTEXT"></a>

Captures the following DDL statements that were run on the system\.

These DDL statements include the following queries and objects: 

+ CREATE SCHEMA, TABLE, VIEW

+ DROP SCHEMA, TABLE, VIEW

+ ALTER SCHEMA, TABLE

See also [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\. These tables provide a timeline of the SQL commands that are executed on the system; this history is useful for troubleshooting purposes and for creating an audit trail of all system activities\.

Use the STARTTIME and ENDTIME columns to find out which statements were logged during a given time period\. Long blocks of SQL text are broken into lines 200 characters long; the SEQUENCE column identifies fragments of text that belong to a single statement\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_DDLTEXT-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_DDLTEXT.html)

## Sample Queries<a name="r_STL_DDLTEXT-sample-queries2"></a>

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