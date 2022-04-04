# Working with data distribution styles<a name="t_Distributing_data"></a>

When you load data into a table, Amazon Redshift distributes the rows of the table to each of the compute nodes according to the table's distribution style\. When you run a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. The goal in choosing a table distribution style is to minimize the impact of the redistribution step by locating the data where it needs to be before the query is run\.

**Note**  
This section will introduce you to the principles of data distribution in an Amazon Redshift database\. We recommend that you create your tables with `DISTSTYLE AUTO`\. If you do so, then Amazon Redshift uses automatic table optimization to choose the data distribution style\. For more information, see [Working with automatic table optimization](t_Creating_tables.md)\. The rest of this section provides details about distribution styles\. 

**Topics**
+ [Data distribution concepts](#t_data_distribution_concepts)
+ [Distribution styles](c_choosing_dist_sort.md)
+ [Viewing distribution styles](viewing-distribution-styles.md)
+ [Evaluating query patterns](t_evaluating_query_patterns.md)
+ [Designating distribution styles](t_designating_distribution_styles.md)
+ [Evaluating the query plan](c_data_redistribution.md)
+ [Query plan example](t_explain_plan_example.md)
+ [Distribution examples](c_Distribution_examples.md)

## Data distribution concepts<a name="t_data_distribution_concepts"></a>

Some data distribution concepts for Amazon Redshift follow\.

 **Nodes and slices** 

 An Amazon Redshift cluster is a set of nodes\. Each node in the cluster has its own operating system, dedicated memory, and dedicated disk storage\. One node is the *leader node*, which manages the distribution of data and query processing tasks to the compute nodes\. The *compute nodes* provide resources to do those tasks\.  

 The disk storage for a compute node is divided into a number of *slices*\. The number of slices per node depends on the node size of the cluster\. For example, each DS2\.XL compute node has two slices, and each DS2\.8XL compute node has 16 slices\. The nodes all participate in running parallel queries, working on data that is distributed as evenly as possible across the slices\. For more information about the number of slices that each node size has, see [About clusters and nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\.

 **Data redistribution** 

 When you load data into a table, Amazon Redshift distributes the rows of the table to each of the node slices according to the table's distribution style\. As part of a query plan, the optimizer determines where blocks of data need to be located to best run the query\. The data is then physically moved, or redistributed, while the query runs\. Redistribution might involve either sending specific rows to nodes for joining or broadcasting an entire table to all of the nodes\. 

 Data redistribution can account for a substantial portion of the cost of a query plan, and the network traffic it generates can affect other database operations and slow overall system performance\. To the extent that you anticipate where best to locate data initially, you can minimize the impact of data redistribution\. 

 **Data distribution goals** 

 When you load data into a table, Amazon Redshift distributes the table's rows to the compute nodes and slices according to the distribution style that you chose when you created the table\. Data distribution has two primary goals: 
+ To distribute the workload uniformly among the nodes in the cluster\. Uneven distribution, or data distribution skew, forces some nodes to do more work than others, which impairs query performance\.
+ To minimize data movement as a query runs\. If the rows that participate in joins or aggregates are already collocated on the nodes with their joining rows in other tables, the optimizer doesn't need to redistribute as much data when queries run\.

The distribution strategy that you choose for your database has important consequences for query performance, storage requirements, data loading, and maintenance\. By choosing the best distribution style for each table, you can balance your data distribution and significantly improve overall system performance\.