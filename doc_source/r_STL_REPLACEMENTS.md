# STL\_REPLACEMENTS<a name="r_STL_REPLACEMENTS"></a>

Displays a log that records when invalid UTF\-8 characters were replaced by the [COPY](r_COPY.md) command with the ACCEPTINVCHARS option\. A log entry is added to STL\_REPLACEMENTS for each of the first 100 rows on each node slice that required at least one replacement\. 

This table is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STL_REPLACEMENTS-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_REPLACEMENTS.html)

## Sample Queries<a name="r_STL_REPLACEMENTS-sample-queries"></a>

The following example returns replacements for the most recent COPY operation\. 

```
select query, session, filename, line_number, colname
from stl_replacements
where query = pg_last_copy_id();

 query | session |   filename                        | line_number | colname
 ------+---------+-----------------------------------+-------------+--------
    96 |    6314 | s3://mybucket/allusers_pipe.txt   |         251 | city
    96 |    6314 | s3://mybucket/allusers_pipe.txt   |         317 | city
    96 |    6314 | s3://mybucket/allusers_pipe.txt   |         569 | city
    96 |    6314 | s3://mybucket/allusers_pipe.txt   |         623 | city
    96 |    6314 | s3://mybucket/allusers_pipe.txt   |         694 | city
...
```