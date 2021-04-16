# Querying external data using Amazon Redshift Spectrum<a name="c-using-spectrum"></a>

Using Amazon Redshift Spectrum, you can efficiently query and retrieve structured and semistructured data from files in Amazon S3 without having to load the data into Amazon Redshift tables\. Redshift Spectrum queries employ massive parallelism to execute very fast against large datasets\. Much of the processing occurs in the Redshift Spectrum layer, and most of the data remains in Amazon S3\. Multiple clusters can concurrently query the same dataset in Amazon S3 without the need to make copies of the data for each cluster\.

**Topics**
+ [Amazon Redshift Spectrum overview](#c-spectrum-overview)
+ [Getting started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md)
+ [IAM policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)
+ [Using Redshift Spectrum with AWS Lake Formation](spectrum-lake-formation.md)
+ [Creating data files for queries in Amazon Redshift Spectrum](c-spectrum-data-files.md)
+ [Creating external schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)
+ [Creating external tables for Amazon Redshift Spectrum](c-spectrum-external-tables.md)
+ [Improving Amazon Redshift Spectrum query performance](c-spectrum-external-performance.md)
+ [Monitoring metrics in Amazon Redshift Spectrum](c-spectrum-metrics.md)
+ [Troubleshooting queries in Amazon Redshift Spectrum](c-spectrum-troubleshooting.md)
+ [Tutorial: Querying nested data with Amazon Redshift Spectrum](tutorial-query-nested-data.md)

## Amazon Redshift Spectrum overview<a name="c-spectrum-overview"></a>

Amazon Redshift Spectrum resides on dedicated Amazon Redshift servers that are independent of your cluster\. Redshift Spectrum pushes many compute\-intensive tasks, such as predicate filtering and aggregation, down to the Redshift Spectrum layer\. Thus, Redshift Spectrum queries use much less of your cluster's processing capacity than other queries\. Redshift Spectrum also scales intelligently\. Based on the demands of your queries, Redshift Spectrum can potentially use thousands of instances to take advantage of massively parallel processing\.

You create Redshift Spectrum tables by defining the structure for your files and registering them as tables in an external data catalog\. The external data catalog can be AWS Glue, the data catalog that comes with Amazon Athena, or your own Apache Hive metastore\. You can create and manage external tables either from Amazon Redshift using data definition language \(DDL\) commands or using any other tool that connects to the external data catalog\. Changes to the external data catalog are immediately available to any of your Amazon Redshift clusters\. 

Optionally, you can partition the external tables on one or more columns\. Defining partitions as part of the external table can improve performance\. The improvement occurs because the Amazon Redshift query optimizer eliminates partitions that don't contain data for the query\. 

After your Redshift Spectrum tables have been defined, you can query and join the tables just as you do any other Amazon Redshift table\. Redshift Spectrum doesn't support update operations on external tables\. You can add Redshift Spectrum tables to multiple Amazon Redshift clusters and query the same data on Amazon S3 from any cluster in the same AWS Region\. When you update Amazon S3 data files, the data is immediately available for query from any of your Amazon Redshift clusters\. 

The AWS Glue Data Catalog that you access might be encrypted to increase security\. If the AWS Glue catalog is encrypted, you need the AWS Key Management Service \(AWS KMS\) key for AWS Glue to access the AWS Glue catalog\. AWS Glue catalog encryption is not available in all AWS Regions\. For a list of supported AWS Regions, see [Encryption and Secure Access for AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/encryption-glue-resources.html) in the *[AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)\. *For more information about AWS Glue Data Catalog encryption, see [Encrypting Your AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *[AWS Glue Developer Guide](https://docs.aws.amazon.com/glue/latest/dg/)\. * 

**Note**  
You can't view details for Redshift Spectrum tables using the same resources that you use for standard Amazon Redshift tables, such as [PG\_TABLE\_DEF](r_PG_TABLE_DEF.md), [STV\_TBL\_PERM](r_STV_TBL_PERM.md), PG\_CLASS, or information\_schema\. If your business intelligence or analytics tool doesn't recognize Redshift Spectrum external tables, configure your application to query [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md)\.

### Amazon Redshift Spectrum Regions<a name="c-spectrum-regions"></a>

Redshift Spectrum is available only in the following AWS Regions: 
+ US East \(N\. Virginia\) Region \(us\-east\-1\)
+ US East \(Ohio\) Region \(us\-east\-2\)
+ US West \(N\. California\) Region \(us\-west\-1\)
+ US West \(Oregon\) Region \(us\-west\-2\) 
+ Asia Pacific \(Hong Kong\) Region \(ap\-east\-1\)
+ Asia Pacific \(Mumbai\) Region \(ap\-south\-1\)
+ Asia Pacific \(Seoul\) Region \(ap\-northeast\-2\)
+ Asia Pacific \(Singapore\) Region \(ap\-southeast\-1\)
+ Asia Pacific \(Sydney\) Region \(ap\-southeast\-2\)
+ Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)
+ Canada \(Central\) Region \(ca\-central\-1\)
+ China \(Beijing\) Region \(cn\-north\-1\)
+ China \(Ningxia\) Region \(cn\-northwest\-1\)
+ Europe \(Frankfurt\) Region \(eu\-central\-1\)
+ Europe \(Ireland\) Region \(eu\-west\-1\)
+ Europe \(London\) Region \(eu\-west\-2\)
+ Europe \(Paris\) Region \(eu\-west\-3\)
+ Europe \(Stockholm\) Region \(eu\-north\-1\)
+ Middle East \(Bahrain\) Region \(me\-south\-1\)
+ South America \(São Paulo\) Region \(sa\-east\-1\)
+ AWS GovCloud \(US\-West\) \(us\-gov\-west\-1\)

### Amazon Redshift Spectrum considerations<a name="c-spectrum-considerations"></a>

Note the following considerations when you use Amazon Redshift Spectrum:
+ The Amazon Redshift cluster and the Amazon S3 bucket must be in the same AWS Region\. 
+ If your cluster uses Enhanced VPC Routing, you might need to perform additional configuration steps\. For more information, see [Using Amazon Redshift Spectrum with Enhanced VPC Routing](https://docs.aws.amazon.com/redshift/latest/mgmt/spectrum-enhanced-vpc.html)\. 
+ You can't perform update or delete operations on external tables\. To create a new external table in the specified schema, you can use CREATE EXTERNAL TABLE\. For more information about CREATE EXTERNAL TABLE, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\. To insert the results of a SELECT query into existing external tables on external catalogs, you can use INSERT \(external table\)\. For more information about INSERT \(external table\), see [INSERT \(external table\)](r_INSERT_external_table.md)\.
+ Unless you are using an AWS Glue Data Catalog that is enabled for AWS Lake Formation, you can't control user permissions on an external table\. Instead, you can grant and revoke permissions on the external schema\. For more information about working with Lake Formation, see [Using Redshift Spectrum with AWS Lake Formation](spectrum-lake-formation.md)\.
+ To run Redshift Spectrum queries, the database user must have permission to create temporary tables in the database\. The following example grants temporary permission on the database `spectrumdb` to the `spectrumusers` user group\. 

  ```
  grant temp on database spectrumdb to group spectrumusers;
  ```

  For more information, see [GRANT](r_GRANT.md)\.
+ When using the Athena Data Catalog or AWS Glue Data Catalog as a metadata store, see [Quotas and Limits](https://docs.aws.amazon.com/redshift/latest/mgmt/amazon-redshift-limits.html) in the *Amazon Redshift Cluster Management Guide\.* 