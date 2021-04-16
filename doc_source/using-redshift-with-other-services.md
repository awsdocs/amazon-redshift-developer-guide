# Using Amazon Redshift with other services<a name="using-redshift-with-other-services"></a>

Amazon Redshift integrates with other AWS services to enable you to move, transform, and load your data quickly and reliably, using data security features\.

## Moving data between Amazon Redshift and Amazon S3<a name="using-redshift-with-s3"></a>

Amazon Simple Storage Service \(Amazon S3\) is a web service that stores data in the cloud\. Amazon Redshift leverages parallel processing to read and load data from multiple data files stored in Amazon S3 buckets\. For more information, see [Loading data from Amazon S3](t_Loading-data-from-S3.md)\.

You can also use parallel processing to export data from your Amazon Redshift data warehouse to multiple data files on Amazon S3\. For more information, see [Unloading data](c_unloading_data.md)\.

## Using Amazon Redshift with Amazon DynamoDB<a name="using-redshift-with-dynamodb"></a>

Amazon DynamoDB is a fully managed NoSQL database service\. You can use the COPY command to load an Amazon Redshift table with data from a single Amazon DynamoDB table\. For more information, see [Loading data from an Amazon DynamoDB table](t_Loading-data-from-dynamodb.md)\.

## Importing data from remote hosts over SSH<a name="using-redshift-with-ssh"></a>

You can use the COPY command in Amazon Redshift to load data from one or more remote hosts, such as Amazon EMR clusters, Amazon EC2 instances, or other computers\. COPY connects to the remote hosts using SSH and executes commands on the remote hosts to generate data\. Amazon Redshift supports multiple simultaneous connections\. The COPY command reads and loads the output from multiple host sources in parallel\. For more information, see [Loading data from remote hosts](loading-data-from-remote-hosts.md)\. 

## Automating data loads using AWS Data Pipeline<a name="using-redshift-with-data-pipeline"></a>

You can use AWS Data Pipeline to automate data movement and transformation into and out of Amazon Redshift\. By using the built\-in scheduling capabilities of AWS Data Pipeline, you can schedule and execute recurring jobs without having to write your own complex data transfer or transformation logic\. For example, you can set up a recurring job to automatically copy data from Amazon DynamoDB into Amazon Redshift\. For a tutorial that walks you through the process of creating a pipeline that periodically moves data from Amazon S3 to Amazon Redshift, see [Copy data to Amazon Redshift using AWS Data Pipeline](https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/dp-copydata-redshift.html) in the AWS Data Pipeline Developer Guide\.

## Migrating data using AWS Database Migration Service \(AWS DMS\)<a name="using-redshift-with-dms"></a>

You can migrate data to Amazon Redshift using AWS Database Migration Service\. AWS DMS can migrate your data to and from most widely used commercial and open\-source databases such as Oracle, PostgreSQL, Microsoft SQL Server, Amazon Redshift, Aurora, DynamoDB, Amazon S3, MariaDB, and MySQL\. For more information, see [Using an Amazon Redshift database as a target for AWS Database Migration Service](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.Redshift.html)\.