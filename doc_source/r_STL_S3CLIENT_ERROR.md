# STL\_S3CLIENT\_ERROR<a name="r_STL_S3CLIENT_ERROR"></a>

Records errors encountered by a slice while loading a file from Amazon S3\.

Use the STL\_S3CLIENT\_ERROR to find details for errors encountered while transferring data from Amazon S3 as part of a COPY command\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_S3CLIENT_ERROR-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_S3CLIENT_ERROR.html)

## Usage notes<a name="w31aac53c13c11c89c11"></a>

If you see multiple errors with "Connection timed out", you might have a networking issue\. If you're using Enhanced VPC Routing, verify that you have a valid network path between your cluster's VPC and your data resources\. For more information, see [Amazon Redshift Enhanced VPC Routing](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html)

## Sample query<a name="w31aac53c13c11c89c13"></a>

The following query returns the errors from COPY commands executed during the current session\.

```
select query, sliceid, substring(key from 1 for 20) as file, 
substring(error from 1 for 35) as error  
from stl_s3client_error 
where pid = pg_backend_pid()
order by query desc;
```

Result

```
 query  | sliceid |     file           |              error                             
--------+---------+--------------------+------------------------------------
 362228 |      12 | part.tbl.25.159.gz | transfer closed with 1947655 bytes 
 362228 |      24 | part.tbl.15.577.gz | transfer closed with 1881910 bytes 
 362228 |       7 | part.tbl.22.600.gz | transfer closed with 700143 bytes r 
 362228 |      22 | part.tbl.3.34.gz   | transfer closed with 2334528 bytes 
 362228 |      11 | part.tbl.30.274.gz | transfer closed with 699031 bytes r
 362228 |      30 | part.tbl.5.509.gz  | Unknown SSL protocol error in conne
 361999 |      10 | part.tbl.23.305.gz | transfer closed with 698959 bytes r
 361999 |      19 | part.tbl.26.582.gz | transfer closed with 1881458 bytes 
 361999 |       4 | part.tbl.15.629.gz | transfer closed with 2275907 bytes 
 361999 |      20 | part.tbl.6.456.gz  | transfer closed with 692162 bytes r
(10 rows)
```