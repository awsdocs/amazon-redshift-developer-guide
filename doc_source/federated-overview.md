# Querying Data with Federated Query in Amazon Redshift<a name="federated-overview"></a>

*Federated queries *in Amazon Redshift enable you to query and analyze data across operational databases, data warehouses, and data lakes\. With the Federated Query feature, you can integrate queries from Amazon Redshift on live data in external databases with queries across your Amazon Redshift and Amazon S3 environments\. Federated queries can work with external databases in Amazon RDS for PostgreSQL and Amazon Aurora with PostgreSQL compatibility\. 

You can use federated queries to incorporate live data as part of your business intelligence \(BI\) and reporting applications\. For example, to make data ingestion to Amazon Redshift easier you can use federated queries to do the following:
+ Query operational databases directly\. 
+ Apply transformations quickly\.
+ Load data into the target tables without the need for complex extract, transform, load \(ETL\) pipelines\.

To reduce data movement over the network and improve performance, Amazon Redshift distributes part of the computation for federated queries directly into the remote operational databases\. Amazon Redshift also uses its parallel processing capacity to support running these queries, as needed\. 

When running federated queries, Amazon Redshift first makes a client connection to the RDS or Aurora PostgreSQL DB instance from the leader node to retrieve table metadata\. From a compute node, Amazon Redshift issues subqueries with a predicate pushed down and retrieves the result rows\. Amazon Redshift then distributes the result rows among the compute nodes for further processing\.

**Topics**
+ [Getting Started with Using Federated Query](getting-started-federated.md)
+ [Creating a Secret and an IAM Role to Use Federated Queries](federated-create-secret-iam-role.md)
+ [Example of Using a Federated Query](federated_query_example.md)
+ [Data Type Differences Between Amazon Redshift and Supported RDS PostgreSQL or Aurora PostgreSQL Databases](federated-data-types.md)
+ [Limitations and Considerations When Accessing Federated Data with Amazon Redshift](federated-limitations.md)