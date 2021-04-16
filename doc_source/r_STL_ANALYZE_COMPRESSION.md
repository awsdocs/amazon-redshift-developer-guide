# STL\_ANALYZE\_COMPRESSION<a name="r_STL_ANALYZE_COMPRESSION"></a>

Records details for compression analysis operations during COPY or ANALYZE COMPRESSION commands\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_ANALYZE_COMPRESSION-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_ANALYZE_COMPRESSION.html)

## Sample queries<a name="r_STL_ANALYZE_COMPRESSION-sample-queries2"></a>

The following example inspects the details of compression analysis on the `lineitem` table by the last COPY command run in the same session\. 

```
select xid, tbl, btrim(tablename) as tablename, col, old_encoding, new_encoding, mode 
from stl_analyze_compression 
where xid = (select xid from stl_query where query = pg_last_copy_id()) order by col;
                
 xid  |  tbl   | tablename | col |  old_encoding   |  new_encoding   |      mode
======+========+===========+=====+=================+=================+=============
 8196 | 248126 | lineitem  |   0 | mostly32        | mostly32        | ON
 8196 | 248126 | lineitem  |   1 | mostly32        | lzo             | ON
 8196 | 248126 | lineitem  |   2 | lzo             | delta32k        | ON
 8196 | 248126 | lineitem  |   3 | delta           | delta           | ON
 8196 | 248126 | lineitem  |   4 | bytedict        | bytedict        | ON
 8196 | 248126 | lineitem  |   5 | mostly32        | mostly32        | ON
 8196 | 248126 | lineitem  |   6 | delta           | delta           | ON
 8196 | 248126 | lineitem  |   7 | delta           | delta           | ON
 8196 | 248126 | lineitem  |   8 | lzo             | zstd            | ON
 8196 | 248126 | lineitem  |   9 | runlength       | zstd            | ON
 8196 | 248126 | lineitem  |  10 | delta           | lzo             | ON
 8196 | 248126 | lineitem  |  11 | delta           | delta           | ON
 8196 | 248126 | lineitem  |  12 | delta           | delta           | ON
 8196 | 248126 | lineitem  |  13 | bytedict        | zstd            | ON
 8196 | 248126 | lineitem  |  14 | bytedict        | zstd            | ON
 8196 | 248126 | lineitem  |  15 | text255         | zstd            | ON
(16 rows)
```