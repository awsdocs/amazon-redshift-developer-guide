# SVL\_QUERY\_SUMMARY<a name="r_SVL_QUERY_SUMMARY"></a>

Use the SVL\_QUERY\_SUMMARY view to find general information about the execution of a query\.

The SVL\_QUERY\_SUMMARY view contains a subset of data from the SVL\_QUERY\_REPORT view\. Note that the information in SVL\_QUERY\_SUMMARY is aggregated from all nodes\. 

**Note**  
 The SVL\_QUERY\_SUMMARY view only contains information about queries executed by Amazon Redshift, not other utility and DDL commands\. For a complete listing and information on all statements executed by Amazon Redshift, including DDL and utility commands, you can query the SVL\_STATEMENTTEXT view\.

SVL\_QUERY\_SUMMARY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

For information about SVCS\_QUERY\_SUMMARY, see [SVCS\_QUERY\_SUMMARY](r_SVCS_QUERY_SUMMARY.md)\.

## Table columns<a name="r_SVL_QUERY_SUMMARY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_QUERY_SUMMARY.html)

## Sample queries<a name="r_SVL_QUERY_SUMMARY-sample-queries"></a>

 **Viewing processing information for a query step** 

The following query shows basic processing information for each step of query 87: 

```
select query, stm, seg, step, rows, bytes
from svl_query_summary
where query = 87
order by query, seg, step;
```

This query retrieves the processing information about query 87, as shown in the following sample output: 

```
 query | stm | seg | step |  rows  |  bytes
-------+-----+-----+------+--------+---------
87     |   0 |   0 |    0 |     90 |    1890 
87     |   0 |   0 |    2 |     90 |     360 
87     |   0 |   1 |    0 |     90 |     360 
87     |   0 |   1 |    2 |     90 |    1440 
87     |   1 |   2 |    0 | 210494 | 4209880 
87     |   1 |   2 |    3 |  89500 |       0 
87     |   1 |   2 |    6 |      4 |      96 
87     |   2 |   3 |    0 |      4 |      96 
87     |   2 |   3 |    1 |      4 |      96 
87     |   2 |   4 |    0 |      4 |      96 
87     |   2 |   4 |    1 |      1 |      24 
87     |   3 |   5 |    0 |      1 |      24 
87     |   3 |   5 |    4 |      0 |       0 
(13 rows)
```

 **Determining whether query steps spilled to disk** 

The following query shows whether or not any of the steps for the query with query ID 1025 \(see the [SVL\_QLOG](r_SVL_QLOG.md) view to learn how to obtain the query ID for a query\) spilled to disk or if the query ran entirely in\-memory: 

```
select query, step, rows, workmem, label, is_diskbased
from svl_query_summary
where query = 1025
order by workmem desc;
```

This query returns the following sample output: 

```
query| step|  rows  |  workmem   |  label        | is_diskbased
-----+-----+--------+-----------+---------------+--------------
1025 |  0  |16000000|  141557760 |scan tbl=9     | f
1025 |  2  |16000000|  135266304 |hash tbl=142   | t
1025 |  0  |16000000|  128974848 |scan tbl=116536| f
1025 |  2  |16000000|  122683392 |dist           | f
(4 rows)
```

By scanning the values for IS\_DISKBASED, you can see which query steps went to disk\. For query 1025, the hash step ran on disk\. Steps might run on disk include hash, aggr, and sort steps\. To view only disk\-based query steps, add **and is\_diskbased = 't'** clause to the SQL statement in the above example\.