# Building a Proof of Concept for Amazon Redshift<a name="proof-of-concept-playbook"></a>

Amazon Redshift is a fast, scalable data warehouse that makes it simple and cost\-effective to analyze all your data using standard SQL and your existing business intelligence tools\. Amazon Redshift delivers 10 times faster performance than other data warehouses\. It does so by using sophisticated query optimization, columnar storage on high\-performance local disks, machine learning, and massively parallel query execution\. 

In the following sections, you can find a framework for building a proof of concept with Amazon Redshift\. The framework gives you architectural best practices for designing and operating a secure, high\-performing, and cost\-effective Amazon Redshift data warehouse\. This guidance is based on reviewing designs of thousands of customers’ architectures across a wide variety of business types and use cases\. We compiled customer experiences to develop this set of best practices to help you identify criteria for evaluating your data warehouse workload\. 

If you are a first\-time user of Amazon Redshift, we recommend that you read [Getting Started with Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html)\. This guide provides a tutorial for using Amazon Redshift to create a sample cluster and work with sample data\. To get insights into the benefits of using Amazon Redshift and into pricing, see [Service Highlights](https://aws.amazon.com/redshift/) and [Pricing Information](https://aws.amazon.com/pricing/) on the marketing webpage\.

## Identifying the Goals of the Proof of Concept<a name="proof-of-concept-goals"></a>

Identifying the goals of the proof of concept plays a critical role in determining what you want to measure as part of the evaluation process\. The evaluation criteria should include the current challenges, enhancements you want to make to improve customer experience, and methods of addressing your current operational pain points\. You can use the following questions to identify the goals of the proof of concept: 
+ Do you have specific service level agreements whose terms you want to improve?
+ What are your goals for scaling your Amazon Redshift data warehouse?
+ What new datasets do you or your customers need to include in your data warehouse?
+ What are the business\-critical SQL queries you need to benchmark? Make sure to include the full range of SQL complexities, such as the different types of queries \(for example, ingest, update, and delete\)\. 
+ What are the general types of workloads you plan to test? Examples might be extract transform load \(ETL\) workloads, reporting queries, and batch extracts\.

After you have answered these questions, you should be able to establish a [SMART](https://en.wikipedia.org/wiki/SMART_criteria) goal for building your proof of concept\.

## Setting Up Your Proof of Concept<a name="setting-up-proof-of-concept"></a>

You set up your Amazon Redshift proof of concept environment in two steps\. First, you set up the AWS resources\. Second, you convert the schema and datasets for evaluation\.

### Designing and Setting Up Your Cluster<a name="choose-redshift-node-type"></a>

You can set up your cluster with either of the following two node types:
+ **Dense Storage**, which enables you to create very large data warehouses using hard disk drives \(HDDs\) for a very low price\. 
+ **Dense Compute**, which enables you to create high\-performance data warehouses using fast CPUs, large amounts of RAM, and solid\-state disks \(SSDs\)\. 

The goals of your workload and your overall budget should help you determine which type of node to select\. Resizing your cluster or switching to a different type of node is simply a button click in the AWS Management Console\. The following additional considerations can help guide you in setting up your cluster: 
+ Select a cluster size that is large enough to handle your production workload\. Generally, you need at least two compute nodes \(a multinode cluster\)\. The leader node is included at no additional cost\.
+ Create your cluster in a virtual private cloud \(VPC\), which provides better performance than an EC2\-Classic installation\. 
+ Plan to maintain at least 20 percent free space, or three times as much memory as needed by your largest table\. This extra space is needed to provide these:
  + Scratch space for usage and rewriting tables
  + Free space required for vacuum operations and for re\-sorting tables
  + Temporary tables used for storing intermediate query results

### Converting Your Schema and Setting Up the Datasets<a name="convert-the-schema"></a>

You can convert your schema, code, and data with either the AWS Schema Conversion Tool \(AWS SCT\) or the AWS Database Migration Service \(AWS DMS\)\. Your best choice of tool depends on the source of your data\. 

The following can help you set up your data in Amazon Redshift:
+ [Migrate from Oracle to Amazon Redshift ](https://aws.amazon.com/getting-started/projects/migrate-oracle-to-amazon-redshift) – This project uses an AWS CloudFormation template, AWS DMS, and AWS SCT to migrate your data with only a few clicks\.
+ [Migrate Your Data Warehouse to Amazon Redshift Using the AWS SCT](https://aws.amazon.com/blogs/database/how-to-migrate-your-data-warehouse-to-amazon-redshift-using-the-aws-schema-conversion-tool-data-extractors/) – This blog provides an overview of how you can use the AWS SCT data extractors to migrate your data warehouse to Amazon Redshift\.

## Cluster Design Considerations<a name="design-considerations"></a>

Keep the following five attributes in mind when designing your cluster\. The SET DW acronym is an easy way to remember them:
+ **S** – The S is for sort key\. Query filters access sort key columns frequently\. Follow these best practices to select sort keys:
  + Choose up to three columns to be the sort key columns
  + Order the sort keys in increasing degree of specificity, but balance this with the frequency of use

  For more guidance on selecting sort keys, see [Choose the Best Sort Key](https://docs.aws.amazon.com/redshift/latest/dg/c_best-practices-sort-key.html) and the AWS Big Data Blog post [The Advanced Table Design Playbook](https://aws.amazon.com/blogs/big-data/amazon-redshift-engineerings-advanced-table-design-playbook-compound-and-interleaved-sort-keys/)\. 
+ **E** – The E is for encoding\. Encoding sets the compression algorithm used for each column in each table\. You can either set encoding yourself, or have Amazon Redshift set this for you\. For more information on how to let Amazon Redshift choose the best compression algorithm, see [Loading tables with Automatic compression](https://docs.aws.amazon.com/redshift/latest/dg/c_Loading_tables_auto_compress.html)\.
+ **T** – The T is for table maintenance\. The Amazon Redshift query optimizer creates more efficient execution plans when query statistics are up\-to\-date\. Use the `ANALYZE` command to gather statistics after loading, updating or deleting data from tables\. Similarly, you can minimize the number of blocks scanned with the `VACUUM` command\. `VACUUM` improves performance by doing the following:
  +  Removing the rows that have been logically deleted from the block, resulting in fewer blocks to scan
  +  Keeping the data in sort key order, which helps target the specific blocks for scanning\.
+ **D** – The D is for table distribution\. You have three options for table distribution:
  + **KEY** – You designate a column for distribution\.
  + **EVEN** – Amazon Redshift assigns the compute nodes with a round\-robin pattern\.
  + **ALL** – Amazon Redshift puts a complete copy of the table in the database slice of each compute node\.
  + The following guidelines can help you select the best distribution pattern: 
    + If users frequently join a `Customers` table using the `customer id` value and doing so distributes the rows evenly across the database slices, then `customer id` is a good choice for a distribution key\.
    + If a table is approximately 5 million rows and contains dimension data, then choose the `ALL` distribution style\.
    + `EVEN` is a safe choice for a distribution pattern, but always results in data distribution across all compute nodes\. 
+ **W** – The W is for Amazon Redshift Workload Management \(WLM\)\. If you use WLM, you control the flow of SQL statements through the compute clusters and how much system memory to allocate\. For more information on setting up WLM, see [Implementing Workload Management](cm-c-implementing-workload-management.md)\.

## Amazon Redshift Evaluation Checklist<a name="amazon-redshift-evaluation-checklist"></a>

For best evaluation results, check the following list of items to determine if they apply to your Amazon Redshift evaluation:
+ **Data load time** – Using the `COPY` command is a common way to test how long it takes to load data\. For more information, see [Best Practices for Loading Data](https://docs.aws.amazon.com/redshift/latest/dg/c_loading-data-best-practices.html)\. 
+ **Throughput of the cluster** – Measuring queries per hour is a common way to determine throughput\. To do so, set up a test to run typical queries for your workload\. 
+ **Data security** – You can easily encrypt data at rest and in transit with Amazon Redshift\. You also have a number of options for managing keys, and Amazon Redshift also supports Single sign\-on \(SSO\) integration\. 
+ **Third\-party tools integration** – You can use either a JDBC or ODBC connection to integrate with business intelligence and other external tools\. 
+ **Interoperability with other AWS services** – Amazon Redshift integrates with other AWS services, such as Amazon EMR, Amazon QuickSight, AWS Glue, Amazon S3 and Kinesis\. You can use this integration in setting up and managing your data warehouse\.
+ **Backing up and making snapshots** – Amazon Redshift automatically backs up your cluster at every 5 GB of changed data, or 8 hours \(whichever occurs first\)\. You can also create a snapshot at any time\.
+ **Using snapshots** – Try using a snapshot and creating a second cluster as part of your evaluation\. Evaluate if your development and testing organizations can use the cluster\.
+ **Resizing** – Your evaluation should include increasing the number or types of Amazon Redshift nodes\. Your cluster remains fully accessible during the resize, although it is in a read\-only mode\. Evaluate if your users can detect that the resize is under way\. 
+ **Support** – We strongly recommend that you evaluate AWS Support as part of your evaluation\.
+ **Offloading queries and accessing infrequently used data** – You can offload your queries to a separate compute layer with Amazon Redshift Spectrum\. You can also easily access infrequently used data directly from S3 without ingesting it into your Amazon Redshift cluster\. 
+ **Operating costs** – Compare the overall cost of operating your data warehouse with other options\. Amazon Redshift is fully managed, and you can perform unlimited analysis of a terabyte of your data for approximately $1000 per year\.

## Benchmarking Your Amazon Redshift Evaluation<a name="amazon-redshift-evaluation-benchmarks"></a>

The following list of possible benchmarks might apply to your Amazon Redshift evaluation:
+ Assemble a list of queries for each runtime category\. Having a sufficient number \(for example, 30 per category\) helps assure that your evaluation reflects a real\-world data warehouse implementation\. 
+ Add a unique identifier to associate each query that you include in your evaluation with one of the categories you establish for your evaluation\. You can then use these unique identifiers to determine throughput for the system tables\. You can also create a `query_group` to organize your evaluation queries\. 

  For example, if you have established a "Reporting" category for your evaluation, you might create a coding system to tag your evaluation queries with the word "Report\." You can then identify individual queries within reporting as R1, R2, and so on\. The following example demonstrates this approach\. 

  ```
  [SELECT "Reporting" as query_category, "R1" as query_id, 
  * FROM customers]
  ```

  When you have associated a query with an evaluation category, you can then use a unique identifier to determine throughput from the system tables for each category\. The following example demonstrates how to do this\.

  ```
  select query, datediff(seconds, starttime, endtime) 
  	from stl_query 
  	where 
  	querytxt like “%Reporting%” 
  	and starttime >= '2018-04-15 00:00' 
  	and endtime <'2018-04-15 23:59'
  ```
+ Test throughput with historical user or ETL queries that have a variety of run times in your existing data warehouse\. Keep the following items in mind when testing throughput:
  + If you are using a load testing utility \(for example an open\-source utility like JMeter, or a custom utility\), make sure that the tool can take the network transmission time into account\. 
  + Make sure that the load testing utility is evaluating execution time based on throughput of the internal system tables in Amazon Redshift\.
+ Identify all the various permutations that you plan to test during your evaluation\. The following list provides some common variables:
  + Cluster size
  + Instance type
  + Load testing duration
  + Concurrency settings
  + WLM configuration

**Need more help? See, [Request Support for your Amazon Redshift Proof\-of\-Concept](https://pages.awscloud.com/redshift-proof-of-concept-request.html)**\.

## Additional Resources<a name="amazon-redshift-evaluation-additional-resources"></a>

To help your Amazon Redshift evaluation, see the following:
+ [Top 10 Performance Tuning Techniques for Amazon Redshift](https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-techniques-for-amazon-redshift/) on the Big Data Blog
+ [Top 8 Best Practices for High\-Performance ETL Processing Using Amazon Redshift](https://aws.amazon.com/blogs/big-data/top-8-best-practices-for-high-performance-etl-processing-using-amazon-redshift/) on the Big Data Blog
+ [Amazon Redshift Management Overview](https://docs.aws.amazon.com/redshift/latest/mgmt/overview.html) in the *Amazon Redshift Cluster Management Guide*
+ [Amazon Redshift Spectrum Getting Started](https://docs.aws.amazon.com/redshift/latest/dg/c-getting-started-using-spectrum.html)