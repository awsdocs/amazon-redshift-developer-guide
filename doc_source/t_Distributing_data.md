# Choosing a data distribution style<a name="t_Distributing_data"></a>

**Topics**
+ [Data distribution concepts](#t_data_distribution_concepts)
+ [Distribution styles](c_choosing_dist_sort.md)
+ [Viewing distribution styles](viewing-distribution-styles.md)
+ [Evaluating query patterns](t_evaluating_query_patterns.md)
+ [Designating distribution styles](t_designating_distribution_styles.md)
+ [Evaluating the query plan](c_data_redistribution.md)
+ [Query plan example](t_explain_plan_example.md)
+ [Distribution examples](c_Distribution_examples.md)

When you load data into a table, Amazon Redshift distributes the rows of the table to each of the compute nodes according to the table's distribution style\. When you run a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. The goal in selecting a table distribution style is to minimize the impact of the redistribution step by locating the data where it needs to be before the query is executed\.

This section will introduce you to the principles of data distribution in an Amazon Redshift database and give you a methodology to choose the best distribution style for each of your tables\.

## Data distribution concepts<a name="t_data_distribution_concepts"></a>

 **Nodes and slices** 

 An Amazon Redshift cluster is a set of nodes\. Each node in the cluster has its own operating system, dedicated memory, and dedicated disk storage\. One node is the *leader node*, which manages the distribution of data and query processing tasks to the *compute nodes*\. 

 The disk storage for a compute node is divided into a number of *slices*\. The number of slices per node depends on the node size of the cluster\. For example, each DS2\.XL compute node has two slices, and each DS2\.8XL compute node has 16 slices\. The nodes all participate in parallel query execution, working on data that is distributed as evenly as possible across the slices\. For more information about the number of slices that each node size has, go to [About clusters and nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\.

 **Data redistribution** 

 When you load data into a table, Amazon Redshift distributes the rows of the table to each of the node slices according to the table's distribution style\. As part of a query plan, the optimizer determines where blocks of data need to be located to best execute the query\. The data is then physically moved, or redistributed, during execution\. Redistribution might involve either sending specific rows to nodes for joining or broadcasting an entire table to all of the nodes\. 

 Data redistribution can account for a substantial portion of the cost of a query plan, and the network traffic it generates can affect other database operations and slow overall system performance\. To the extent that you anticipate where best to locate data initially, you can minimize the impact of data redistribution\. 

 **Data distribution goals** 

 When you load data into a table, Amazon Redshift distributes the table's rows to the compute nodes and slices according to the distribution style that you chose when you created the table\. Data distribution has two primary goals: 
+ To distribute the workload uniformly among the nodes in the cluster\. Uneven distribution, or data distribution skew, forces some nodes to do more work than others, which impairs query performance\.
+ To minimize data movement during query execution\. If the rows that participate in joins or aggregates are already collocated on the nodes with their joining rows in other tables, the optimizer does not need to redistribute as much data during query execution\.

The distribution strategy that you choose for your database has important consequences for query performance, storage requirements, data loading, and maintenance\. By choosing the best distribution style for each table, you can balance your data distribution and significantly improve overall system performance\.