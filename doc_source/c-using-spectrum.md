# Using Amazon Redshift Spectrum to Query External Data<a name="c-using-spectrum"></a>

Using Amazon Redshift Spectrum, you can efficiently query and retrieve structured and semistructured data from files in Amazon S3 without having to load the data into Amazon Redshift tables\. Redshift Spectrum queries employ massive parallelism to execute very fast against large datasets\. Much of the processing occurs in the Redshift Spectrum layer, and most of the data remains in Amazon S3\. Multiple clusters can concurrently query the same dataset in Amazon S3 without the need to make copies of the data for each cluster\.


+ [Amazon Redshift Spectrum Overview](#c-spectrum-overview)
+ [Getting Started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md)
+ [IAM Policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)
+ [Creating Data Files for Queries in Amazon Redshift Spectrum](c-spectrum-data-files.md)
+ [Creating External Schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)
+ [Creating External Tables for Amazon Redshift Spectrum](c-spectrum-external-tables.md)
+ [Improving Amazon Redshift Spectrum Query Performance](c-spectrum-external-performance.md)
+ [Monitoring Metrics in Amazon Redshift Spectrum](c-spectrum-metrics.md)
+ [Troubleshooting Queries in Amazon Redshift Spectrum](c-spectrum-troubleshooting.md)

## Amazon Redshift Spectrum Overview<a name="c-spectrum-overview"></a>

Amazon Redshift Spectrum resides on dedicated Amazon Redshift servers that are independent of your cluster\. Redshift Spectrum pushes many compute\-intensive tasks, such as predicate filtering and aggregation, down to the Redshift Spectrum layer\. Thus, Redshift Spectrum queries use much less of your cluster's processing capacity than other queries\. Redshift Spectrum also scales intelligently\. Based on the demands of your queries, Redshift Spectrum can potentially use thousands of instances to take advantage of massively parallel processing\.

You create Redshift Spectrum tables by defining the structure for your files and registering them as tables in an external data catalog\. The external data catalog can be AWS Glue, the data catalog that comes with Amazon Athena, or your own Apache Hive metastore\. You can create and manage external tables either from Amazon Redshift using data definition language \(DDL\) commands or using any other tool that connects to the external data catalog\. Changes to the external data catalog are immediately available to any of your Amazon Redshift clusters\. 

Optionally, you can partition the external tables on one or more columns\. Defining partitions as part of the external table can improve performance\. The improvement occurs because the Amazon Redshift query optimizer eliminates partitions that don’t contain data for the query\. 

After your Redshift Spectrum tables have been defined, you can query and join the tables just as you do any other Amazon Redshift table\. Amazon Redshift doesn't support update operations on external tables\. You can add Redshift Spectrum tables to multiple Amazon Redshift clusters and query the same data on Amazon S3 from any cluster in the same region\. When you update Amazon S3 data files, the data is immediately available for query from any of your Amazon Redshift clusters\. 

**Note**  
You can't view details for Redshift Spectrum tables using the same resources that you use for standard Amazon Redshift tables, such as [PG\_TABLE\_DEF](r_PG_TABLE_DEF.md), [STV\_TBL\_PERM](r_STV_TBL_PERM.md), PG\_CLASS, or information\_schema\. If your business intelligence or analytics tool doesn't recognize Redshift Spectrum external tables, configure your application to query [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md)\.

### Amazon Redshift Spectrum Regions<a name="c-spectrum-regions"></a>

Redshift Spectrum is available only in the following regions: 

+ US East \(N\. Virginia\) Region \(us\-east\-1\)

+ US East \(Ohio\) Region \(us\-east\-2\)

+ US West \(N\. California\) Region \(us\-west\-1\)

+ US West \(Oregon\) Region \(us\-west\-2\) 

+ Asia Pacific \(Seoul\) Region \(ap\-northeast\-2\)

+ Asia Pacific \(Singapore\) Region \(ap\-southeast\-1\)

+ Asia Pacific \(Sydney\) Region \(ap\-southeast\-2\)

+ Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)

+ Canada \(Central\) Region \(ca\-central\-1\)

+ EU \(Frankfurt\) Region \(eu\-central\-1\)

+ EU \(Ireland\) Region \(eu\-west\-1\)

+ EU \(London\) Region \(eu\-west\-2\)

### Amazon Redshift Spectrum Considerations<a name="c-spectrum-considerations"></a>

Note the following considerations when you use Amazon Redshift Spectrum:

+ Amazon Redshift Spectrum requires engine version 1\.0\.1294 or later\. To find the version number for your cluster, run the following command\.

  To force your cluster to update to the latest cluster version, adjust your [maintenance window](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-maintenance-windows)\. 

+ The Amazon Redshift cluster and the Amazon S3 bucket must be in the same region\. 

+ Your cluster can't have [Enhanced VPC Routing](http://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html) enabled\. 

+ External tables are read\-only\. You can't perform insert, update, or delete operations on external tables\. 

+ You can't control user permissions on an external table\. Instead, you can grant and revoke permissions on the external schema\. 

+ Redshift Spectrum doesn't support nested data types, such as STRUCT, ARRAY, and MAP\.

+ When using the Athena data catalog, the following limits apply:

  + A maximum of 100 databases per account\.

  + A maximum of 100 tables per database\.

  + A maximum of 20,000 partitions per table\.

  You can request a limit increase by contacting AWS Support\.

  These limits don’t apply to an Apache Hive metastore\.

  For more information, see [Creating External Schemas for Amazon Redshift Spectrum](c-spectrum-external-schemas.md)\.