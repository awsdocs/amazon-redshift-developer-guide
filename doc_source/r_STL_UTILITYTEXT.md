# STL\_UTILITYTEXT<a name="r_STL_UTILITYTEXT"></a>

Captures the text of non\-SELECT SQL commands run on the database\.

Query the STL\_UTILITYTEXT table to capture the following subset of SQL statements that were run on the system:
+ ABORT, BEGIN, COMMIT, END, ROLLBACK
+ CANCEL
+ COMMENT
+ CREATE, ALTER, DROP DATABASE
+ CREATE, ALTER, DROP USER
+ EXPLAIN
+ GRANT, REVOKE
+ LOCK
+ RESET
+ SET
+ SHOW
+ TRUNCATE

See also [STL\_DDLTEXT](r_STL_DDLTEXT.md), [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\.

Use the STARTTIME and ENDTIME columns to find out which statements were logged during a given time period\. Long blocks of SQL text are broken into lines 200 characters long; the SEQUENCE column identifies fragments of text that belong to a single statement\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_UTILITYTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_UTILITYTEXT.html)

## Sample Queries<a name="r_STL_UTILITYTEXT-sample-queries"></a>

The following query returns the text for "utility" commands that were run on January 26th, 2012\. In this case, some SET commands and a SHOW ALL command were run: 

```
select starttime, sequence, rtrim(text)
from stl_utilitytext
where starttime like '2012-01-26%'
order by starttime, sequence;

starttime          | sequence |              rtrim
---------------------------+-----+----------------------------------
2012-01-26 13:05:52.529235 |   0 | show all;
2012-01-26 13:20:31.660255 |   0 | SET query_group to ''
2012-01-26 13:20:54.956131 |   0 | SET query_group to 'soldunsold.sql'
...
```