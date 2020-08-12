# STL\_QUERYTEXT<a name="r_STL_QUERYTEXT"></a>

Captures the query text for SQL commands\.

Query the STL\_QUERYTEXT view to capture the SQL that was logged for the following statements: 
+ SELECT, SELECT INTO
+ INSERT, UPDATE, DELETE
+ COPY
+ UNLOAD
+ VACUUM, ANALYZE
+ CREATE TABLE AS \(CTAS\)

To query activity for these statements over a given time period, join the STL\_QUERYTEXT and STL\_QUERY views\.

**Note**  
The STL\_QUERY and STL\_QUERYTEXT views only contain information about queries, not other utility and DDL commands\. For a listing and information on all statements executed by Amazon Redshift, you can also query the STL\_DDLTEXT and STL\_UTILITYTEXT views\. For a complete listing of all statements executed by Amazon Redshift, you can query the SVL\_STATEMENTTEXT view\.

See also [STL\_DDLTEXT](r_STL_DDLTEXT.md), [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_QUERYTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_QUERYTEXT.html)

## Sample queries<a name="r_STL_QUERYTEXT-sample-queries"></a>

You can use the PG\_BACKEND\_PID\(\) function to retrieve information for the current session\. For example, the following query returns the query ID and a portion of the query text for queries executed in the current session\.

```
select query, substring(text,1,60)
from stl_querytext
where pid = pg_backend_pid()
order by query desc;

 query |                         substring
-------+--------------------------------------------------------------
 28262 | select query, substring(text,1,80) from stl_querytext where 
 28252 | select query, substring(path,0,80) as path from stl_unload_l
 28248 | copy category from 's3://dw-tickit/manifest/category/1030_ma
 28247 | Count rows in target table
 28245 | unload ('select * from category') to 's3://dw-tickit/manifes
 28240 | select query, substring(text,1,40) from stl_querytext where 
(6 rows)
```

### Reconstructing stored SQL<a name="r_STL_QUERYTEXT-reconstruct-sql"></a>

To reconstruct the SQL stored in the `text` column of STL\_QUERYTEXT, run a SELECT statement to create SQL from 1 or more parts in the `text` column\. Before running the reconstructed SQL, replace any \(`\n`\) special characters with a new line\. The result of the following SELECT statement is rows of reconstructed SQL in the `query_statement` field\.

```
SELECT query, LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END) WITHIN GROUP (ORDER BY sequence) as query_statement, COUNT(*) as row_count 
FROM stl_querytext GROUP BY query ORDER BY query desc;
```

For example, the following query selects 3 columns\. The query itself is longer than 200 characters and is stored in parts in STL\_QUERYTEXT\.

```
select
1 AS a0123456789012345678901234567890123456789012345678901234567890,
2 AS b0123456789012345678901234567890123456789012345678901234567890,
3 AS b012345678901234567890123456789012345678901234
FROM stl_querytext;
```

In this example, the query is stored in 2 parts \(rows\) in the `text` column of STL\_QUERYTEXT\.

```
select query, sequence, text
from stl_querytext where query=pg_last_query_id() order by query desc, sequence limit 10;
```

```
query  | sequence |                                                                                             text                                                                                                   
-------+----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    45 |        0 | select\n1 AS a0123456789012345678901234567890123456789012345678901234567890,\n2 AS b0123456789012345678901234567890123456789012345678901234567890,\n3 AS b012345678901234567890123456789012345678901234
    45 |        1 | \nFROM stl_querytext;
```

To reconstruct the SQL stored in STL\_QUERYTEXT, run the following SQL\. 

```
select LISTAGG(CASE WHEN LEN(RTRIM(text)) = 0 THEN text ELSE RTRIM(text) END, '') within group (order by sequence) AS text 
from stl_querytext where query=pg_last_query_id();
```

To use the resulting reconstructed SQL in your client, replace any \(`\n`\) special characters with a new line\. 

```
                                                                                                             text                                                                                                             
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 select\n1 AS a0123456789012345678901234567890123456789012345678901234567890,\n2 AS b0123456789012345678901234567890123456789012345678901234567890,\n3 AS b012345678901234567890123456789012345678901234\nFROM stl_querytext;
```