# Step 3: Create an external schema and an external table<a name="c-getting-started-using-spectrum-create-external-table"></a>

Create external tables in an external schema\. The external schema references a database in the external data catalog and provides the IAM role ARN that authorizes your cluster to access Amazon S3 on your behalf\. You can create an external database in an Amazon Athena Data Catalog, AWS Glue Data Catalog, or an Apache Hive metastore, such as Amazon EMR\. For this example, you create the external database in an Amazon Athena Data Catalog when you create the external schema Amazon Redshift\. For more information, see [Creating external schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)\. <a name="spectrum-get-started-create-external-table"></a>

**To create an external schema and an external table**

1. To create an external schema, replace the IAM role ARN in the following command with the role ARN you created in [step 1](c-getting-started-using-spectrum-create-role.md)\. Then run the command in your SQL client\.

   ```
   create external schema myspectrum_schema 
   from data catalog 
   database 'myspectrum_db' 
   iam_role 'arn:aws:iam::123456789012:role/myspectrum_role'
   create external database if not exists;
   ```

1. To create an external table, run the following CREATE EXTERNAL TABLE command\.
**Note**  
Your cluster and the Amazon S3 bucket must be in the same AWS Region\. For this example CREATE EXTERNAL TABLE command, the Amazon S3 bucket with the sample data is located in the US East \(N\. Virginia\) AWS Region\. To see the source data, download the [`sales_ts.000` file](https://s3.amazonaws.com/redshift-downloads/tickit/spectrum/sales/sales_ts.000)\.   
You can modify this example to run in a different AWS Region\. Create an Amazon S3 bucket in your desired AWS Region\. Copy the sales data with an Amazon S3 copy command\. Then update the location option in the example `CREATE EXTERNAL TABLE` command to your bucket\.  

   ```
   aws s3 cp s3://redshift-downloads/tickit/spectrum/sales/ s3://bucket-name/tickit/spectrum/sales/ --copy-props none --recursive
   ```
The output of the Amazon S3 copy command confirms that the file was copied to the *bucket\-name* in your desired AWS Region\.  

   ```
   copy: s3://redshift-downloads/tickit/spectrum/sales/sales_ts.000 to s3://bucket-name/tickit/spectrum/sales/sales_ts.000
   ```

   ```
   create external table myspectrum_schema.sales(
   salesid integer,
   listid integer,
   sellerid integer,
   buyerid integer,
   eventid integer,
   dateid smallint,
   qtysold smallint,
   pricepaid decimal(8,2),
   commission decimal(8,2),
   saletime timestamp)
   row format delimited
   fields terminated by '\t'
   stored as textfile
   location 's3://redshift-downloads/tickit/spectrum/sales/'
   table properties ('numRows'='172000');
   ```