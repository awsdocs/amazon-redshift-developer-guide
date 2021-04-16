# STL\_UTILITYTEXT<a name="r_STL_UTILITYTEXT"></a>

Captures the text of non\-SELECT SQL commands run on the database\.

Query the STL\_UTILITYTEXT view to capture the following subset of SQL statements that were run on the system:
+ ABORT, BEGIN, COMMIT, END, ROLLBACK
+ CALL
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

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_UTILITYTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_UTILITYTEXT.html)

## Sample queries<a name="r_STL_UTILITYTEXT-sample-queries"></a>

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

### Reconstructing Stored SQL<a name="r_STL_UTILITYTEXT-reconstruct-sql"></a>

To reconstruct the SQL stored in the `text` column of STL\_UTILITYTEXT, run a SELECT statement to create SQL from 1 or more parts in the `text` column\. Before running the reconstructed SQL, replace any \(`\n`\) special characters with a new line\. The result of the following SELECT statement is rows of reconstructed SQL in the `query_statement` field\.

```
SELECT LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END) WITHIN GROUP (ORDER BY sequence) as query_statement 
FROM stl_utilitytext GROUP BY xid order by xid;
```

For example, the following query sets the query\_group to a string of zeros\. The query itself is longer than 200 characters and is stored in parts in STL\_UTILITYTEXT\.

```
set query_group to '00000000000000000000000000000000000000000000000000000000000000000000000000000000
0000000000000000000000000000000000000000000000000000000000000000000000000000000000                  000000';
```

In this example, the query is stored in 2 parts \(rows\) in the `text` column of STL\_UTILITYTEXT\.

```
select query, sequence, text
from stl_utilitytext where query=pg_last_query_id() order by query desc, sequence limit 10;
```

```
         starttime          | sequence |                                                                                                   text                                                                                                   
----------------------------+----------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 2019-07-23 22:55:34.926198 |        0 | set query_group to '00000000000000000000000000000000000000000000000000000000000000000000000000000000\n0000000000000000000000000000000000000000000000000000000000000000000000000000000000
 2019-07-23 22:55:34.926198 |        1 |                   000000';
```

To reconstruct the SQL stored in STL\_UTILITYTEXT, run the following SQL\. 

```
select LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END, '') within group (order by sequence) AS query_statement 
from stl_utilitytext where query=pg_last_query_id();
```

To use the resulting reconstructed SQL in your client, replace any \(`\n`\) special characters with a new line\. 

```
                                                                                                                                      query_statement                                                                                                                                       
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 set query_group to '00000000000000000000000000000000000000000000000000000000000000000000000000000000\n0000000000000000000000000000000000000000000000000000000000000000000000000000000000                  000000';
```