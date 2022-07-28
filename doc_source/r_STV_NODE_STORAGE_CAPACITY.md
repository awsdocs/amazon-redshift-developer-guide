# STV\_NODE\_STORAGE\_CAPACITY<a name="r_STV_NODE_STORAGE_CAPACITY"></a>

The STV\_NODE\_STORAGE\_CAPACITY table shows details of total storage capacity and total used capacity for each node in a cluster\. It contains a row for each node\. 

STV\_NODE\_STORAGE\_CAPACITY is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_NODE_STORAGE_CAPACITY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_NODE_STORAGE_CAPACITY.html)

## Sample queries<a name="r_STV_NODE_STORAGE_CAPACITY-sample-query"></a>

**Note**  
The results of the following examples vary based on the node specifications of your cluster\. Add column `capacity` to your SQL SELECT to retrieve the capacity of your cluster\. 

The following query returns used space and total capacity in 1 MB disk blocks\. This example ran on a two\-node dc2\.8xlarge cluster\. 

```
select node, used from stv_node_storage_capacity order by node;
```

This query returns the following sample output\. 

```
 
 node | used  
------+-------
    0 | 30597 
    1 | 27089
```

The following query returns used space and total capacity in 1 MB disk blocks\. This example ran on a two\-node ra3\.16xlarge cluster\.  

```
select node, used from stv_node_storage_capacity order by node;
```

This query returns the following sample output\. 

```
 
 node | used  
------+-------
    0 | 30591 
    1 | 27103
```