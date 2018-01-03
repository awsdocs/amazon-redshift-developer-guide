# Step 3: Create an External Schema and an External Table<a name="c-getting-started-using-spectrum-create-external-table"></a>

External tables must be created in an external schema\. The external schema references a database in the external data catalog and provides the IAM role ARN that authorizes your cluster to access Amazon S3 on your behalf\. You can create an external database in an Amazon Athena data catalog or an Apache Hive metastore, such as Amazon EMR\. For this example, you create the external database in an Amazon Athena data catalog when you create the external schema Amazon Redshift\. For more information, see [Creating External Schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)\. 

**To create an external schema and an external table**

1. To create an external schema, replace the IAM role ARN in the following command with the role ARN you created in step 1, and then execute the command in your SQL client\.

   ```
   create external schema spectrum 
   from data catalog 
   database 'spectrumdb' 
   iam_role 'arn:aws:iam::123456789012:role/mySpectrumRole'
   create external database if not exists;
   ```

1. To create an external table, run the following CREATE EXTERNAL TABLE command\.
**Note**  
The Amazon S3 bucket with the sample data for this example is located in the us\-west\-2 region\. Your cluster and the Redshift Spectrum files must be in the same region, so, for this example, your cluster must also be located in us\-west\-2\.

   ```
   create external table spectrum.sales(
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
   location 's3://awssampledbuswest2/tickit/spectrum/sales/'
   table properties ('numRows'='172000');
   ```