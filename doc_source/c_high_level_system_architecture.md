# Data warehouse system architecture<a name="c_high_level_system_architecture"></a>

This section introduces the elements of the Amazon Redshift data warehouse architecture as shown in the following figure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/02-NodeRelationships.png)

 **Client applications** 

Amazon Redshift integrates with various data loading and ETL \(extract, transform, and load\) tools and business intelligence \(BI\) reporting, data mining, and analytics tools\. Amazon Redshift is based on industry\-standard PostgreSQL, so most existing SQL client applications will work with only minimal changes\. For information about important differences between Amazon Redshift SQL and PostgreSQL, see [Amazon Redshift and PostgreSQL](c_redshift-and-postgres-sql.md)\.

 **Connections** 

Amazon Redshift communicates with client applications by using industry\-standard JDBC and ODBC drivers for PostgreSQL\. For more information, see [Amazon Redshift and PostgreSQL JDBC and ODBC](c_redshift-postgres-jdbc.md)\.

 **Clusters** 

The core infrastructure component of an Amazon Redshift data warehouse is a *cluster*\.

A cluster is composed of one or more *compute nodes*\. If a cluster is provisioned with two or more compute nodes, an additional *leader node* coordinates the compute nodes and handles external communication\. Your client application interacts directly only with the leader node\. The compute nodes are transparent to external applications\.

 **Leader node** 

The leader node manages communications with client programs and all communication with compute nodes\. It parses and develops execution plans to carry out database operations, in particular, the series of steps necessary to obtain results for complex queries\. Based on the execution plan, the leader node compiles code, distributes the compiled code to the compute nodes, and assigns a portion of the data to each compute node\.

The leader node distributes SQL statements to the compute nodes only when a query references tables that are stored on the compute nodes\. All other queries run exclusively on the leader node\. Amazon Redshift is designed to implement certain SQL functions only on the leader node\. A query that uses any of these functions will return an error if it references tables that reside on the compute nodes\. For more information, see [SQL functions supported on the leader node](c_sql-functions-leader-node.md)\.

 **Compute nodes** 

The leader node compiles code for individual elements of the execution plan and assigns the code to individual compute nodes\. The compute nodes execute the compiled code and send intermediate results back to the leader node for final aggregation\. 

Each compute node has its own dedicated CPU, memory, and attached disk storage, which are determined by the node type\. As your workload grows, you can increase the compute capacity and storage capacity of a cluster by increasing the number of nodes, upgrading the node type, or both\.

Amazon Redshift provides several node types for your compute and storage needs\. For details of each node type, see [Amazon Redshift clusters](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html) in the *Amazon Redshift Cluster Management Guide*\. 

 **Node slices** 

A compute node is partitioned into slices\. Each slice is allocated a portion of the node's memory and disk space, where it processes a portion of the workload assigned to the node\. The leader node manages distributing data to the slices and apportions the workload for any queries or other database operations to the slices\. The slices then work in parallel to complete the operation\.

The number of slices per node is determined by the node size of the cluster\. For more information about the number of slices for each node size, go to [About clusters and nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\.

When you create a table, you can optionally specify one column as the distribution key\. When the table is loaded with data, the rows are distributed to the node slices according to the distribution key that is defined for a table\. Choosing a good distribution key enables Amazon Redshift to use parallel processing to load data and execute queries efficiently\. For information about choosing a distribution key, see [Choose the best distribution style](c_best-practices-best-dist-key.md)\.

 **Internal network** 

Amazon Redshift takes advantage of high\-bandwidth connections, close proximity, and custom communication protocols to provide private, very high\-speed network communication between the leader node and compute nodes\. The compute nodes run on a separate, isolated network that client applications never access directly\.

 **Databases** 

A cluster contains one or more databases\. User data is stored on the compute nodes\. Your SQL client communicates with the leader node, which in turn coordinates query execution with the compute nodes\.

Amazon Redshift is a relational database management system \(RDBMS\), so it is compatible with other RDBMS applications\. Although it provides the same functionality as a typical RDBMS, including online transaction processing \(OLTP\) functions such as inserting and deleting data, Amazon Redshift is optimized for high\-performance analysis and reporting of very large datasets\.

Amazon Redshift is based on PostgreSQL\. Amazon Redshift and PostgreSQL have a number of very important differences that you need to take into account as you design and develop your data warehouse applications\. For information about how Amazon Redshift SQL differs from PostgreSQL, see [Amazon Redshift and PostgreSQL](c_redshift-and-postgres-sql.md)\.