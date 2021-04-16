# STV\_SLICES<a name="r_STV_SLICES"></a>

Use the STV\_SLICES table to view the current mapping of a slice to a node\.

 The information in STV\_SLICES is used mainly for investigation purposes\.

STV\_SLICES is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\. 

## Table columns<a name="r_STV_SLICES-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_SLICES.html)

## Sample query<a name="r_STV_SLICES-sample-query2"></a>

To view which cluster nodes are managing which slices, type the following query:

```
select node, slice from stv_slices;
```

This query returns the following sample output:

```
 node | slice
------+-------
    0 |     2
    0 |     3
    0 |     1
    0 |     0
(4 rows)
```