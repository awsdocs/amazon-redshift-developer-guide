# Conducting a proof of concept for Amazon Redshift<a name="proof-of-concept-playbook"></a>

Amazon Redshift is a fast, scalable data warehouse that makes it simple and cost\-effective to analyze all your data using standard SQL with your existing business intelligence \(BI\) tools\. Amazon Redshift offers fast performance in a low\-cost cloud data warehouse\. It uses sophisticated query optimization, accelerated cache, columnar storage on high\-performance local disks, and massively parallel query execution\. 

In the following sections, you can find a framework for building a proof of concept with Amazon Redshift\. The framework helps you to use architectural best practices for designing and operating a secure, high\-performing, and cost\-effective data warehouse\. This guidance is based on reviewing designs of thousands of customer architectures across a wide variety of business types and use cases\. We have compiled customer experiences to develop this set of best practices to help you develop criteria for evaluating your data warehouse workload\. 

## Overview of the process<a name="proof-of-concept-overview"></a>

Conducting a proof of concept is a three\-step process:

1. Identify the goals of the proof of concept – you can work backward from your business requirements and success criteria, and translate them into a technical proof of concept project plan\. 

1. Set up the proof of concept environment – most of the setup process is a click of few buttons to create your resources\. Within minutes, you can have a data warehouse environment ready with data loaded\. 

1. Execute the proof of concept project plan to ensure that the goals are met\. 

In the following sections, we go into the details of each step\.

## Identify the business goals and success criteria<a name="proof-of-concept-goals"></a>

Identifying the goals of the proof of concept plays a critical role in determining what you want to measure as part of the evaluation process\. The evaluation criteria should include the current scaling challenges, enhancements to improve your customer's experience of the data warehouse, and methods of addressing your current operational pain points\. You can use the following questions to identify the goals of the proof of concept: 
+ What are your goals for scaling your data warehouse? 
+ What are the specific service\-level agreements whose terms you want to improve? 
+ What new datasets do you need to include in your data warehouse? 
+ What are the business\-critical SQL queries that you need to test and measure? Make sure to include the full range of SQL complexities, such as the different types of queries \(for example, select, insert, update, and delete\)\. 
+ What are the general types of workloads you plan to test? Examples might include extract\-transform\-load \(ETL\) workloads, reporting queries, and batch extracts\. 

After you have answered these questions, you should be able to establish SMART goals and success criteria for building your proof of concept\. For information about setting goals, see [SMART criteria](https://en.wikipedia.org/wiki/SMART_criteria) in *Wikipedia*\. 

## Set up your proof of concept<a name="proof-of-concept-set-up"></a>

Because we eliminated hardware provisioning, networking, and software installation from an on\-premises data warehouse, trying Amazon Redshift with your own dataset has never been easier\. Many of the sizing decisions and estimations that used to be required are now simply a click away\. You can flexibly resize your cluster or adjust the ratio of storage versus compute\. 

Broadly, setting up the Amazon Redshift proof of concept environment is a two\-step process\. It involves the launching of a data warehouse and then the conversion of the schema and datasets for evaluation\.

### Choose a starting cluster size<a name="proof-of-concept-choose-node-type"></a>

You can choose the node type and number of nodes using the Amazon Redshift console\. We recommend that you also test resizing the cluster as part of your proof of concept plan\. To get the initial sizing for your cluster, take the following steps:

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation pane, choose **Create cluster** to open the configuration page\. 

1. For **Cluster identifier**, enter a name for your cluster\. 

1. Choose one of the following methods to size your cluster:
**Note**  
The following step describes an Amazon Redshift console that is running in an AWS Region that supports RA3 node types\. For a list of AWS Regions that support RA3 node types, see [Overview of RA3 node types](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-ra3-node-types) in the *Amazon Redshift Cluster Management Guide*\. 
   + If your AWS Region supports RA3 node types, choose either **Production** or **Free trial** to answer the question **What are you planning to use this cluster for?** 

     If your organization is eligible, you might be able to create a cluster under the Amazon Redshift free trial program\. To do this, choose **Free trial** to create a configuration with the dc2\.large node type\. For more information about choosing a free trial, see [Amazon Redshift free trial](http://aws.amazon.com/redshift/free-trial/)\. 
   + If you don't know how large to size your cluster, choose **Help me choose**\. Doing this starts a sizing calculator that asks you questions about the size and query characteristics of the data that you plan to store in your data warehouse\. 

     If you know the required size of your cluster \(that is, the node type and number of nodes\), choose **I'll choose**\. Then choose the **Node type** and number of **Nodes** to size your cluster for the proof of concept\.

1. After you enter all required cluster properties, choose **Create cluster** to launch your data warehouse\. 

   For more details about creating clusters with the Amazon Redshift console, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the *Amazon Redshift Cluster Management Guide*\. 

### Convert the schema and set up the datasets for the proof of concept<a name="proof-of-concept-convert-schema"></a>

If you don't have an existing data warehouse, skip this section and see [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/)\. *Amazon Redshift Getting Started* provides a tutorial to create a cluster and examples of setting up data in Amazon Redshift\. 

When migrating from your existing data warehouse, you can convert schema, code, and data using the AWS Schema Conversion Tool and the AWS Database Migration Service\. Your choice of tools depends on the source of your data and optional ongoing replications\. For more information, see [What Is the AWS Schema Conversion Tool?](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Welcome.html) in the *AWS Schema Conversion Tool User Guide* and [What Is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) in the *AWS Database Migration Service User Guide*\. The following can help you set up your data in Amazon Redshift: 
+ [Migrate Your Data Warehouse to Amazon Redshift Using the AWS Schema Conversion Tool](https://aws.amazon.com/blogs/database/how-to-migrate-your-data-warehouse-to-amazon-redshift-using-the-aws-schema-conversion-tool-data-extractors/) – this blog post provides an overview on how you can use the AWS SCT data extractors to migrate your existing data warehouse to Amazon Redshift\. The AWS SCT tool can migrate your data from many legacy platforms \(such as Oracle, Greenplum, Netezza, Teradata, Microsoft SQL Server, or Vertica\)\. 
+ Optionally, you can also use the AWS Database Migration Service for ongoing replications of changed data from the source\. For more information, see [Using an Amazon Redshift Database as a Target for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Redshift.html) in the *AWS Database Migration Service User Guide*\. 

Amazon Redshift is a relational database management system \(RDBMS\)\. As such, it can run many types of data models including star schemas, snowflake schemas, data vault models, and simple, flat, or normalized tables\. After setting up your schemas in Amazon Redshift, you can take advantage of massively parallel processing and columnar data storage for fast analytical queries out of the box\. For information about types of schemas, see [star schema](https://en.wikipedia.org/wiki/Star_schema), [snowflake schema](https://en.wikipedia.org/wiki/Snowflake_schema), and [data vault modeling](https://en.wikipedia.org/wiki/Data_vault_modeling) in *Wikipedia*\. 

## Checklist for a complete evaluation<a name="proof-of-concept-evaluation-checklist"></a>

Make sure that a complete evaluation meets all your data warehouse needs\. Consider including the following items in your success criteria: 
+ **Data load time** – using the `COPY` command is a common way to test how long it takes to load data\. For more information, see [Amazon Redshift best practices for loading data](c_loading-data-best-practices.md)\. 
+ **Throughput of the cluster** – measuring queries per hour is a common way to determine throughput\. To do so, set up a test to run typical queries for your workload\. 
+ **Data security** – you can easily encrypt data at rest and in transit with Amazon Redshift\. You also have a number of options for managing keys\. Amazon Redshift also supports single sign\-on \(SSO\) integration\. Amazon Redshift pricing includes built\-in security, data compression, backup storage, and data transfer\.
+ **Third\-party tools integration** – you can use either a JDBC or ODBC connection to integrate with business intelligence and other external tools\. 
+ **Interoperability with other AWS services** – Amazon Redshift integrates with other AWS services, such as Amazon EMR, Amazon QuickSight, AWS Glue, Amazon S3, and Amazon Kinesis\. You can use this integration when setting up and managing your data warehouse\.
+ **Backups and snapshots** – backups and snapshots are created automatically\. You can also create a point\-in\-time snapshot at any time or on a schedule\. Try using a snapshot and creating a second cluster as part of your evaluation\. Evaluate if your development and testing organizations can use the cluster\. 
+ **Resizing** – your evaluation should include increasing the number or types of Amazon Redshift nodes\. Evaluate that the workload throughput before and after a resize meets any variability of the volume of your workload\. For more information, see [Resizing clusters in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-cluster-operations.html#rs-resize-tutorial) in the *Amazon Redshift Cluster Management Guide*\. 
+ **Concurrency scaling** – this feature helps you handle variability of traffic volume in your data warehouse\. With concurrency scaling, you can support virtually unlimited concurrent users and concurrent queries, with consistently fast query performance\. For more information, see [Working with concurrency scaling](concurrency-scaling.md)\. 
+ **Automatic workload management \(WLM\)** – prioritize your business critical queries over other queries by using automatic WLM\. Try setting up queues based on your workloads \(for example, a queue for ETL and a queue for reporting\)\. Then enable automatic WLM to allocate the concurrency and memory resources dynamically\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\. 
+ **Amazon Redshift Advisor** – the Advisor develops customized recommendations to increase performance and optimize costs by analyzing your workload and usage metrics for your cluster\. Sign in to the Amazon Redshift console to view Advisor recommendations\. For more information, see [Working with recommendations from Amazon Redshift Advisor](advisor.md)\.
+ **Table design** – Amazon Redshift provides great performance out of the box for most workloads\. To get even better performance for your queries, check the Amazon Redshift Advisor in the Amazon Redshift console\. Advisor shows recommendations on setting optimal sort and distribution keys for your tables\. Because Advisor needs time to assess your workload, it usually takes a couple of days to get these recommendations\. For more information about designing tables, see [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md)\. 
+ **Support** – we strongly recommend that you evaluate AWS Support as part of your evaluation\. Also, make sure to talk to your account manager about your proof of concept\. AWS can help with technical guidance and credits for the proof of concept if you qualify\. If you don't find the help you're looking for, you can talk directly to the Amazon Redshift team\. For help, submit the form at [Request support for your Amazon Redshift proof\-of\-concept](https://pages.awscloud.com/redshift-proof-of-concept-request.html)\. 
+ **Lake house integration** – with built\-in integration, try using the out\-of\-box Amazon Redshift Spectrum feature\. With Redshift Spectrum, you can extend the data warehouse into your data lake and run queries against petabytes of data in Amazon S3 using your existing cluster\. For more information, see [Querying external data using Amazon Redshift Spectrum](c-using-spectrum.md)\. 

## Develop a project plan for your evaluation<a name="proof-of-concept-evaluation-plan"></a>

Some of the following techniques for creating query benchmarks might help support your Amazon Redshift evaluation:
+ Assemble a list of queries for each runtime category\. Having a sufficient number \(for example, 30 per category\) helps ensure that your evaluation reflects a real\-world data warehouse implementation\. Add a unique identifier to associate each query that you include in your evaluation with one of the categories you establish for your evaluation\. You can then use these unique identifiers to determine throughput from the system tables\. 

  You can also create a query group to organize your evaluation queries\. For example, if you have established a "Reporting" category for your evaluation, you might create a coding system to tag your evaluation queries with the word "Report\." You can then identify individual queries within reporting as R1, R2, and so on\. The following example demonstrates this approach\.

  ```
  SELECT 'Reporting' AS query_category, 'R1' as query_id, * FROM customers;              
  ```

  ```
  SELECT query, datediff(seconds, starttime, endtime)
    FROM stl_query 
    WHERE 
    querytxt LIKE '%Reporting%' 
    and starttime >= '2018-04-15 00:00' 
    and endtime < '2018-04-15 23:59';
  ```

  When you have associated a query with an evaluation category, you can use a unique identifier to determine throughput from the system tables for each category\. 
+ Test throughput with historical user or ETL queries that have a variety of runtimes in your existing data warehouse\. You might use a load testing utility, such as the open\-source [JMeter](https://jmeter.apache.org/) or a custom utility\. If so, make sure that your utility does the following:
  + It can take the network transmission time into account\. 
  + It evaluates execution time based on throughput of the internal system tables\. For information about how to do this, see [Analyzing the query summary](c-analyzing-the-query-summary.md)\. 
+ Identify all the various permutations that you plan to test during your evaluation\. The following list provides some common variables:
  + Cluster size
  + Node type
  + Load testing duration
  + Concurrency settings
+ Reduce the cost of your proof of concept by pausing your cluster during off\-hours and weekends\. When a cluster is paused, on\-demand compute billing is suspended\. To run tests on the cluster, resume per\-second billing\. You can also create a schedule to pause and resume your cluster automatically\. For more information, see [Pausing and resuming clusters](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-cluster-operations.html#rs-mgmt-pause-resume-cluster) in the *Amazon Redshift Cluster Management Guide*\. 

At this stage, you're ready to execute on your project plan and evaluate results\. 

## Additional resources to help your evaluation<a name="proof-of-concept-evaluation-additional-resources"></a>

To help your Amazon Redshift evaluation, see the following:
+ [Service highlights and pricing](https://aws.amazon.com/redshift/) – this product detail page provides the Amazon Redshift value proposition, service highlights, and pricing\.
+ [Amazon Redshift Getting Started](http://docs.aws.amazon.com/redshift/latest/gsg/) – this guide provides a tutorial of using Amazon Redshift to create a sample cluster and work with sample data\.
+ [Getting started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md) – in this tutorial, you learn how to use Redshift Spectrum to query data directly from files on Amazon S3\. 
+ [Amazon Redshift management overview](http://docs.aws.amazon.com/redshift/latest/mgmt/overview.html) – this topic in the *Amazon Redshift Cluster Management Guide* provides an overview of Amazon Redshift\. 
+ Optimize Amazon Redshift for performance with BI tools – consider integration with tools such as [Tableau](https://www.tableau.com/learn/whitepapers/optimize-tableau-redshift-better-deployment), [Power BI](https://aws.amazon.com/blogs/big-data/integrate-power-bi-with-amazon-redshift-for-insights-and-analytics/), and others\. 
+  [Amazon Redshift Advisor recommendations](advisor-recommendations.md) – contains explanations and details for each Advisor recommendation\. 
+ [What's new in Amazon Redshift](https://aws.amazon.com/redshift/whats-new/) – announcements that help you keep track of new features and enhancements\. 
+ [Improved speed and scalability](https://aws.amazon.com/blogs/big-data/improved-speed-and-scalability-in-amazon-redshift/) – this blog post summarizes recent Amazon Redshift improvements\. 

## Need help?<a name="proof-of-concept-help"></a>

Make sure to talk to your account manager to let them know about your proof of concept\. AWS can help with technical guidance and credits for the proof of concept if you qualify\. If you don't find the help you are looking for, you can talk directly to the Amazon Redshift team\. For help, submit the form at [Request support for your Amazon Redshift proof\-of\-concept](https://pages.awscloud.com/redshift-proof-of-concept-request.html)\. 