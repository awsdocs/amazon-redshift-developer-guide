# Analyzing table design<a name="c_analyzing-table-design"></a>

As you have seen in the previous sections, specifying sort keys, distribution keys, and column encodings can significantly improve storage, I/O, and query performance\. This section provides a SQL script that you can run to help you identify tables where these options are missing or performing poorly\. 

Copy and paste the following code to create a SQL script named `table_inspector.sql`, then execute the script in your SQL client application as superuser\. 

```
SELECT SCHEMA schemaname,
       "table" tablename,
       table_id tableid,
       size size_in_mb,
       CASE
         WHEN diststyle NOT IN ('EVEN','ALL') THEN 1
         ELSE 0
       END has_dist_key,
       CASE
         WHEN sortkey1 IS NOT NULL THEN 1
         ELSE 0
       END has_sort_key,
       CASE
         WHEN encoded = 'Y' THEN 1
         ELSE 0
       END has_col_encoding,
       CAST(max_blocks_per_slice - min_blocks_per_slice AS FLOAT) / GREATEST(NVL (min_blocks_per_slice,0)::int,1) ratio_skew_across_slices,
       CAST(100*dist_slice AS FLOAT) /(SELECT COUNT(DISTINCT slice) FROM stv_slices) pct_slices_populated
FROM svv_table_info ti
  JOIN (SELECT tbl,
               MIN(c) min_blocks_per_slice,
               MAX(c) max_blocks_per_slice,
               COUNT(DISTINCT slice) dist_slice
        FROM (SELECT b.tbl,
                     b.slice,
                     COUNT(*) AS c
              FROM STV_BLOCKLIST b
              GROUP BY b.tbl,
                       b.slice)
        WHERE tbl IN (SELECT table_id FROM svv_table_info)
        GROUP BY tbl) iq ON iq.tbl = ti.table_id;
```

The following sample shows the results of running the script with two sample tables, SKEW1 and SKEW2, that demonstrate the effects of data skew\.

```
 
          |         |       |     |has_  |has_ |has_    |ratio_skew|pct_
          |         |       |size_|dist_ |sort_|col_    |_across_  |slices_
schemaname|tablename|tableid|in_mb|key   |key  |encoding|slices    |populated
----------+---------+-------+-----+------+-----+--------+----------+---------
public    |category |100553 |  28 |    1 |   1 |      0 |        0 |      100
public    |date     |100555 |  44 |    1 |   1 |      0 |        0 |      100
public    |event    |100558 |  36 |    1 |   1 |      1 |        0 |      100
public    |listing  |100560 |  44 |    1 |   1 |      1 |        0 |      100
public    |nation   |100563 | 175 |    0 |   0 |      0 |        0 |    39.06
public    |region   |100566 |  30 |    0 |   0 |      0 |        0 |     7.81
public    |sales    |100562 |  52 |    1 |   1 |      0 |        0 |      100
public    |skew1    |100547 |18978|    0 |   0 |      0 |      .15 |       50
public    |skew2    |100548 | 353 |    1 |   0 |      0 |        0 |     1.56
public    |venue    |100551 |  32 |    1 |   1 |      0 |        0 |      100
public    |users    |100549 |  82 |    1 |   1 |      1 |        0 |      100
public    |venue    |100551 |  32 |    1 |   1 |      0 |        0 |      100
```

The following list describes the columns in the result:

 **has\_dist\_key**   
Indicates whether the table has distribution key\. 1 indicates a key exists; 0 indicates there is no key\. For example, `nation` does not have a distribution key \.

 **has\_sort\_key**   
Indicates whether the table has a sort key\. 1 indicates a key exists; 0 indicates there is no key\. For example, `nation` does not have a sort key\.

 **has\_column\_encoding**   
Indicates whether the table has any compression encodings defined for any of the columns\. 1 indicates at least one column has an encoding\. 0 indicates there is no encoding\. For example, `region` has no compression encoding\.

 **ratio\_skew\_across\_slices**   
An indication of the data distribution skew\. A smaller value is good\. 

 **pct\_slices\_populated**   
The percentage of slices populated\. A larger value is good\.

Tables for which there is significant data distribution skew will have either a large value in the ratio\_skew\_across\_slices column or a small value in the pct\_slices\_populated column\. This indicates that you have not chosen an appropriate distribution key column\. In the example above, the SKEW1 table has a \.15 skew ratio across slices, but that's not necessarily a problem\. What's more significant is the 1\.56% value for the slices populated for the SKEW2 table\. The small value is an indication that the SKEW2 table has the wrong distribution key\.

Run the `table_inspector.sql` script whenever you add new tables to your database or whenever you have significantly modified your tables\.