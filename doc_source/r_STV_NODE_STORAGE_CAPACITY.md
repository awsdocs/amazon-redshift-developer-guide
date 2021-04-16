# STV\_NODE\_STORAGE\_CAPACITY<a name="r_STV_NODE_STORAGE_CAPACITY"></a>

The STV\_NODE\_STORAGE\_CAPACITY table shows details of total storage capacity and total used capacity for each node in a cluster\. It contains a row for each node\. 

STV\_NODE\_STORAGE\_CAPACITY is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_NODE_STORAGE_CAPACITY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_NODE_STORAGE_CAPACITY.html)

## Sample queries<a name="r_STV_NODE_STORAGE_CAPACITY-sample-query"></a>

The results of the following examples vary based on the node specifications of your cluster\. 

The following query returns used space and total capacity in 1 MB disk blocks\. This example ran on a two\-node dc2\.8xlarge cluster\. Total capacity is 3,043,824 MB per node\. 

```
select node, used, capacity from stv_node_storage_capacity order by node;
```

This query returns the following sample output\. 

```
 node | used  | capacity
------+-------+----------
    0 | 30597 |  3043824
    1 | 27089 |  3043824
```

The following query returns used space and total capacity in 1 MB disk blocks\. This example ran on a two\-node ra3\.16xlarge cluster\. The capacity is 64 TB per node\. 

```
select node, used, capacity from stv_node_storage_capacity order by node;
```

This query returns the following sample output\. 

```
 node | used  | capacity
------+-------+----------
    0 | 30591 | 64000000
    1 | 27103 | 64000000
```