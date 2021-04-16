# Querying data with federated queries in Amazon Redshift<a name="federated-overview"></a>

By using *federated queries* in Amazon Redshift, you can query and analyze data across operational databases, data warehouses, and data lakes\. With the Federated Query feature, you can integrate queries from Amazon Redshift on live data in external databases with queries across your Amazon Redshift and Amazon S3 environments\. Federated queries can work with external databases in Amazon RDS for PostgreSQL, Amazon Aurora with PostgreSQL compatibility, Amazon RDS for MySQL \(preview\), and Amazon Aurora with MySQL compatibility \(preview\)\. 

You can use federated queries to incorporate live data as part of your business intelligence \(BI\) and reporting applications\. For example, to make data ingestion to Amazon Redshift easier you can use federated queries to do the following:
+ Query operational databases directly\. 
+ Apply transformations quickly\.
+ Load data into the target tables without the need for complex extract, transform, load \(ETL\) pipelines\.

To reduce data movement over the network and improve performance, Amazon Redshift distributes part of the computation for federated queries directly into the remote operational databases\. Amazon Redshift also uses its parallel processing capacity to support running these queries, as needed\. 

When running federated queries, Amazon Redshift first makes a client connection to the RDS or Aurora DB instance from the leader node to retrieve table metadata\. From a compute node, Amazon Redshift issues subqueries with a predicate pushed down and retrieves the result rows\. Amazon Redshift then distributes the result rows among the compute nodes for further processing\.

Details about queries sent to the Amazon Aurora PostgreSQL database or Amazon RDS for PostgreSQL database are logged in the system view [SVL\_FEDERATED\_QUERY](r_SVL_FEDERATED_QUERY.md)\.

**Topics**
+ [Getting started with using federated queries to PostgreSQL](getting-started-federated.md)
+ [Getting started with using federated queries to MySQL \(preview\)](getting-started-federated-mysql.md)
+ [Creating a secret and an IAM role to use federated queries](federated-create-secret-iam-role.md)
+ [Examples of using a federated query](federated_query_example.md)
+ [Data type differences between Amazon Redshift and supported PostgreSQL and MySQL databases](federated-data-types.md)
+ [Limitations and considerations when accessing federated data with Amazon Redshift](federated-limitations.md)