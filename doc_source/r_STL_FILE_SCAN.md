# STL\_FILE\_SCAN<a name="r_STL_FILE_SCAN"></a>

Returns the files that Amazon Redshift read while loading data via the COPY command\.

Querying this view can help troubleshoot data load errors\. STL\_FILE\_SCAN can be particularly helpful with pinpointing issues in parallel data loads because parallel data loads typically load many files with a single COPY command\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_FILE_SCAN-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_FILE_SCAN.html)

## Sample queries<a name="r_STL_FILE_SCAN-sample-queries2"></a>

The following query retrieves the names and load times of any files that took over 1000000 microseconds for Amazon Redshift to read:

```
select trim(name)as name, loadtime from stl_file_scan
where loadtime > 1000000;
```

This query returns the following example output:

```
           name            | loadtime
---------------------------+----------
 listings_pipe.txt         |  9458354
 allusers_pipe.txt         |  2963761
 allevents_pipe.txt        |  1409135
 tickit/listings_pipe.txt  |  7071087
 tickit/allevents_pipe.txt |  1237364
 tickit/allusers_pipe.txt  |  2535138
 listings_pipe.txt         |  6706370
 allusers_pipe.txt         |  3579461
 allevents_pipe.txt        |  1313195
 tickit/allusers_pipe.txt  |  3236060
 tickit/listings_pipe.txt  |  4980108
(11 rows)
```