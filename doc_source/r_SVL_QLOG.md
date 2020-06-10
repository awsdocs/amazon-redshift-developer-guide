# SVL\_QLOG<a name="r_SVL_QLOG"></a>

The SVL\_QLOG view contains a log of all queries run against the database\.

Amazon Redshift creates the SVL\_QLOG view as a readable subset of information from the [STL\_QUERY](r_STL_QUERY.md) table\. Use this table to find the query ID for a recently run query or to see how long it took a query to complete\.

SVL\_QLOG is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_QLOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_QLOG.html)

## Sample queries<a name="r_SVL_QLOG-sample-queries"></a>

The following example returns the query ID, execution time, and truncated query text for the five most recent database queries executed by the user with `userid = 100`\.

```
select query, pid, elapsed, substring from svl_qlog
where userid = 100
order by starttime desc
limit 5;

 query  |  pid  | elapsed  |           substring
--------+-------+----------+-----------------------------------------------
 187752 | 18921 | 18465685 | select query, elapsed, substring from svl_...
 204168 |  5117 |    59603 | insert into testtable values (100);
 187561 | 17046 |  1003052 | select * from pg_table_def where tablename...
 187549 | 17046 |  1108584 | select * from STV_WLM_SERVICE_CLASS_CONFIG
 187468 | 17046 |  5670661 | select * from pg_table_def where schemaname...
(5 rows)
```

The following example returns the SQL script name \(LABEL column\) and elapsed time for a query that was cancelled \(**aborted=1**\): 

```
select query, elapsed, trim(label) querylabel
from svl_qlog where aborted=1;
 
 query | elapsed  |       querylabel
-------+----------+-------------------------
    16 |  6935292 | alltickittablesjoin.sql
(1 row)
```