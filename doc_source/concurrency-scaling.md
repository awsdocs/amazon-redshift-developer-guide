# Working with concurrency scaling<a name="concurrency-scaling"></a>

With the Concurrency Scaling feature, you can support virtually unlimited concurrent users and concurrent queries, with consistently fast query performance\.

When you turn on concurrency scaling, Amazon Redshift automatically adds additional cluster capacity to process an increase in both read queries and write queries\. Users see the most current data, whether the queries run on the main cluster or a concurrency\-scaling cluster\. You're charged for concurrency\-scaling clusters only for the time they're actively running queries\. For more information about pricing, see [Amazon Redshift pricing](https://aws.amazon.com/redshift/pricing/)\. 

You manage which queries are sent to the concurrency\-scaling cluster by configuring WLM queues\. When you turn on concurrency scaling, eligible queries are sent to the concurrency\-scaling cluster instead of waiting in a queue\.

## Concurrency scaling capabilities<a name="concurrency-scaling-capabilities"></a>

When you turn on concurrency scaling for a WLM queue, it works for read operations, such as dashboard queries\. It also works for commonly used write operations, such as statements for data ingestion and processing\. 

### Concurrency scaling capabilities for write operations<a name="concurrency-scaling-capabilities-write-operations"></a>

Concurrency scaling supports frequently used write operations, such as extract, transform, and load \(ETL\) statements\. Concurrency scaling for write operations is especially useful when you want to maintain consistent response times when your cluster receives a large number of requests\. It improves throughput for write operations contending for resources on the main cluster\. It doesn't require any WLM queue configuration changes\.

Concurrency scaling supports COPY, INSERT, DELETE, and UPDATE statements\. In some cases, you might follow DDL statements, such as CREATE, with write statements in the same commit block\. In these cases, the write statements are not sent to the concurrency\-scaling cluster\.

When you accrue credit for concurrency scaling, this credit accrual applies to both read and write operations\.

## Limitations for concurrency scaling<a name="concurrency-scaling-limitations"></a>

 The following are limitations for using Amazon Redshift concurrency scaling: 
+ It doesn't support queries on tables that use interleaved sort keys\.
+ It doesn't support queries on temporary tables\.
+ It doesn't support queries that access external resources that are protected by restrictive network or virtual private cloud \(VPC\) configurations\.
+ It doesn't support queries that contain Python user\-defined functions \(UDFs\)\.
+ It doesn't support queries that access system tables, PostgreSQL catalog tables, or no\-backup tables\.
+ It doesn’t support COPY or UNLOAD queries that access an external resource that has restrictive resource policies\. External resources can include Amazon S3 buckets or DynamoDB tables\. Policies can restrict access to a specific source VPC \(`aws:sourceVpc`\), source VPC endpoint \(`aws:sourceVpce`\), or source IP address \(`aws:SourceIp`\)\. In some cases, you might need to remove policies on an external resource\. Doing this means that COPY or UNLOAD queries accessing the resource are sent to the concurrency\-scaling cluster\.
+ Amazon Redshift concurrency scaling for write operations is not supported for DDL operations, such as CREATE TABLE or ALTER TABLE\. 
+ It doesn't support ANALYZE for the COPY command\.
+ It doesn't support write operations on a target table where DISTSTYLE is set to ALL\.
+ It doesn't support COPY from Amazon Redshift Spectrum or Amazon EMR\.
+ It doesn't support write operations on tables with identity columns\.
+ Amazon Redshift supports concurrency scaling for write operations on only Amazon Redshift RA3 nodes, specifically ra3\.16xlarge, ra3\.4xlarge, and ra3\.xlplus\. Concurrency scaling for write operations isn't supported on other node types\.

For more information about concurrency scaling, see the following topics in the *Amazon Redshift Cluster Management Guide*\.
+ [Viewing Concurrency Scaling Data](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-concurrency-scaling.html) 
+ [Viewing Cluster Performance During Query Execution](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-query-cluster.html) 
+ [Viewing Query Details](https://docs.aws.amazon.com/redshift/latest/mgmt/performance-metrics-query-execution-details.html) 