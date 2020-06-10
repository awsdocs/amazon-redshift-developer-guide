# SVL\_STATEMENTTEXT<a name="r_SVL_STATEMENTTEXT"></a>

Use the SVL\_STATEMENTTEXT view to get a complete record of all of the SQL commands that have been run on the system\.

The SVL\_STATEMENTTEXT view contains the union of all of the rows in the [STL\_DDLTEXT](r_STL_DDLTEXT.md), [STL\_QUERYTEXT](r_STL_QUERYTEXT.md), and [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md) tables\. This view also includes a join to the STL\_QUERY table\.

SVL\_STATEMENTTEXT is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_STATEMENTTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_STATEMENTTEXT.html)

## Sample query<a name="r_SVL_STATEMENTTEXT-sample-query"></a>

The following query returns DDL statements that were run on June 16th, 2009: 

```
select starttime, type, rtrim(text) from svl_statementtext
where starttime like '2009-06-16%' and type='DDL' order by starttime asc;

starttime                  | type |              rtrim
---------------------------|------|--------------------------------
2009-06-16 10:36:50.625097 | DDL  | create table ddltest(c1 int);
2009-06-16 15:02:16.006341 | DDL  | drop view alltickitjoin;
2009-06-16 15:02:23.65285  | DDL  | drop table sales;
2009-06-16 15:02:24.548928 | DDL  | drop table listing;
2009-06-16 15:02:25.536655 | DDL  | drop table event;
...
```

### Reconstructing stored SQL<a name="r_SVL_STATEMENTTEXT-reconstruct-sql"></a>

To reconstruct the SQL stored in the `text` column of SVL\_STATEMENTTEXT, run a SELECT statement to create SQL from 1 or more parts in the `text` column\. Before running the reconstructed SQL, replace any \(`\n`\) special characters with a new line\. The result of the following SELECT statement is rows of reconstructed SQL in the `query_statement` field\.

```
select LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END, '') within group (order by sequence) AS query_statement 
from SVL_STATEMENTTEXT where pid=pg_backend_pid();
```

For example, the following query selects 3 columns\. The query itself is longer than 200 characters and is stored in parts in SVL\_STATEMENTTEXT\.

```
select
1 AS a0123456789012345678901234567890123456789012345678901234567890,
2 AS b0123456789012345678901234567890123456789012345678901234567890,
3 AS b012345678901234567890123456789012345678901234
FROM stl_querytext;
```

In this example, the query is stored in 2 parts \(rows\) in the `text` column of SVL\_STATEMENTTEXT\.

```
select sequence, text from SVL_STATEMENTTEXT where pid = pg_backend_pid() order by starttime, sequence;
```

```
 sequence |                                                                                             text                                                                                                   
----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
        0 | select\n1 AS a0123456789012345678901234567890123456789012345678901234567890,\n2 AS b0123456789012345678901234567890123456789012345678901234567890,\n3 AS b012345678901234567890123456789012345678901234
        1 | \nFROM stl_querytext;
```

To reconstruct the SQL stored in STL\_STATEMENTTEXT, run the following SQL\. 

```
select LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END, '') within group (order by sequence) AS text 
from SVL_STATEMENTTEXT where pid=pg_backend_pid();
```

To use the resulting reconstructed SQL in your client, replace any \(`\n`\) special characters with a new line\. 

```
                                                                                                             text                                                                                                             
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 select\n1 AS a0123456789012345678901234567890123456789012345678901234567890,\n2 AS b0123456789012345678901234567890123456789012345678901234567890,\n3 AS b012345678901234567890123456789012345678901234\nFROM stl_querytext;
```