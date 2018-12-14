# STL\_QUERYTEXT<a name="r_STL_QUERYTEXT"></a>

Captures the query text for SQL commands\.

Query the STL\_QUERYTEXT table to capture the SQL that was logged for the following statements: 
+ SELECT, SELECT INTO
+ INSERT, UPDATE, DELETE
+ COPY
+ VACUUM, ANALYZE
+ CREATE TABLE AS \(CTAS\)

To query activity for these statements over a given time period, join the STL\_QUERYTEXT and STL\_QUERY tables\.

**Note**  
The STL\_QUERY and STL\_QUERYTEXT tables only contain information about queries, not other utility and DDL commands\. For a listing and information on all statements executed by Amazon Redshift, you can also query the STL\_DDLTEXT and STL\_UTILITYTEXT tables\. For a complete listing of all statements executed by Amazon Redshift, you can query the SVL\_STATEMENTTEXT view\.

See also [STL\_DDLTEXT](r_STL_DDLTEXT.md), [STL\_UTILITYTEXT](r_STL_UTILITYTEXT.md), and [SVL\_STATEMENTTEXT](r_SVL_STATEMENTTEXT.md)\.

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_QUERYTEXT-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_QUERYTEXT.html)

## Sample Queries<a name="r_STL_QUERYTEXT-sample-queries"></a>

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