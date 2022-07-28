# SVL\_SPATIAL\_SIMPLIFY<a name="r_SVL_SPATIAL_SIMPLIFY"></a>

You can query the system view SVL\_SPATIAL\_SIMPLIFY to get information about simplified spatial geometry objects using the COPY command\. When you use COPY on a shapefile, you can specify SIMPLIFY `tolerance`, SIMPLIFY AUTO, and SIMPLIFY AUTO `max_tolerance` ingestion options\. The result of the simplification is summarized in SVL\_SPATIAL\_SIMPLIFY system view\. 

When SIMPLIFY AUTO `max_tolerance` is set, this view contains a row for each geometry that exceeded the maximum size\. When SIMPLIFY `tolerance` is set, then one row for the entire COPY operation is stored\. This row references the COPY query ID and the specified simplification tolerance\.

SVL\_SPATIAL\_SIMPLIFY is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_SPATIAL_SIMPLIFY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_SPATIAL_SIMPLIFY.html)

## Sample query<a name="r_SVL_SPATIAL_SIMPLIFY-sample-query"></a>

The following query returns the list of records that COPY simplified\. 

```
SELECT * FROM svl_spatial_simplify WHERE query = pg_last_copy_id();
 query | line_number | maximum_tolerance | initial_size | simplified | final_size |   final_tolerance
-------+-------------+-------------------+--------------+------------+------------+----------------------
    20 |     1184704 |                -1 |      1513736 | t          |    1008808 |   1.276386653895e-05
    20 |     1664115 |                -1 |      1233456 | t          |    1023584 | 6.11707814796635e-06
```