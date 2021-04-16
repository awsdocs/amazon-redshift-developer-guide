# Step 4: Load sample data<a name="cm-dev-t-load-sample-data"></a>

Most of the examples in this guide use the TICKIT sample database\. If you want to follow the examples using your SQL query tool, you will need to load the sample data for the TICKIT database\.

The sample data for this tutorial is provided in Amazon S3 buckets that give read access to all authenticated AWS users, so any valid AWS credentials that permit access to Amazon S3 will work\. 

To load the sample data for the TICKIT database, you will first create the tables, then use the COPY command to load the tables with sample data that is stored in an Amazon S3 bucket\. For steps to create tables and load sample data, see [Amazon Redshift getting started guide](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html)\.