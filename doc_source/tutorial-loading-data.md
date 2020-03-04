# Tutorial: Loading Data from Amazon S3<a name="tutorial-loading-data"></a>

In this tutorial, you walk through the process of loading data into your Amazon Redshift database tables from data files in an Amazon S3 bucket from beginning to end\. 

In this tutorial, you do the following: 
+ Download data files that use comma\-separated value \(CSV\), character\-delimited, and fixed width formats\. 
+ Create an Amazon S3 bucket and then upload the data files to the bucket\. 
+ Launch an Amazon Redshift cluster and create database tables\. 
+ Use COPY commands to load the tables from the data files on Amazon S3\. 
+ Troubleshoot load errors and modify your COPY commands to correct the errors\.

**Estimated time:** 60 minutes

**Estimated cost:** $1\.00 per hour for the cluster

## Prerequisites<a name="tutorial-loading-data-prerequisites"></a>

You need the following prerequisites:
+ An AWS account to launch an Amazon Redshift cluster and to create a bucket in Amazon S3\.
+ Your AWS credentials \(an access key ID and secret access key\) to load test data from Amazon S3\. If you need to create new access keys, go to [Administering Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html)\.
+ An SQL client such as the Amazon Redshift console query editor\. 

This tutorial is designed so that it can be taken by itself\. In addition to this tutorial, we recommend completing the following tutorials to gain a more complete understanding of how to design and use Amazon Redshift databases: 
+ [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/) walks you through the process of creating an Amazon Redshift cluster and loading sample data\. 
+ [Tutorial: Tuning Table Design](tutorial-tuning-tables.md) walks you step by step through the process of designing and tuning tables, including choosing sort keys, distribution styles, and compression encodings, and evaluating system performance before and after tuning\.

## Overview<a name="tutorial-loading-data-overview"></a>

You can add data to your Amazon Redshift tables either by using an INSERT command or by using a COPY command\. At the scale and speed of an Amazon Redshift data warehouse, the COPY command is many times faster and more efficient than INSERT commands\. 

The COPY command uses the Amazon Redshift massively parallel processing \(MPP\) architecture to read and load data in parallel from multiple data sources\. You can load from data files on Amazon S3, Amazon EMR, or any remote host accessible through a Secure Shell \(SSH\) connection\. Or you can load directly from an Amazon DynamoDB table\. 

In this tutorial, you use the COPY command to load data from Amazon S3\. Many of the principles presented here apply to loading from other data sources as well\. 

To learn more about using the COPY command, see these resources: 
+ [Amazon Redshift Best Practices for Loading Data](c_loading-data-best-practices.md)
+ [Loading Data from Amazon EMR](loading-data-from-emr.md)
+ [Loading Data from Remote Hosts](loading-data-from-remote-hosts.md)
+ [Loading Data from an Amazon DynamoDB Table](t_Loading-data-from-dynamodb.md)

## Steps<a name="tutorial-loading-data-steps"></a>
+ [Step 1: Create a Cluster](tutorial-loading-data-launch-cluster.md)
+ [Step 2: Download the Data Files](tutorial-loading-data-download-files.md)
+ [Step 3: Upload the Files to an Amazon S3 Bucket](tutorial-loading-data-upload-files.md)
+ [Step 4: Create the Sample Tables](tutorial-loading-data-create-tables.md)
+ [Step 5: Run the COPY Commands](tutorial-loading-run-copy.md)
+ [Step 6: Vacuum and Analyze the Database](tutorial-loading-data-vacuum.md)
+ [Step 7: Clean Up Your Resources](tutorial-loading-data-clean-up.md)