# Getting Started with Amazon Redshift Spectrum<a name="c-getting-started-using-spectrum"></a>

In this tutorial, you learn how to use Amazon Redshift Spectrum to query data directly from files on Amazon S3\. If you already have a cluster and a SQL client, you can complete this tutorial in ten minutes or less\.

**Note**  
Redshift Spectrum queries incur additional charges\. The cost of running the sample queries in this tutorial is nominal\. For more information about pricing, see [ Redshift Spectrum Pricing](https://aws.amazon.com/redshift/pricing/#redshift-spectrum-pricing)\.

## Prerequisites<a name="c-getting-started-using-spectrum-prerequisites"></a>

To use Redshift Spectrum, you need an Amazon Redshift cluster and a SQL client that's connected to your cluster so that you can execute SQL commands\. The cluster and the data files in Amazon S3 must be in the same AWS Region\. For this example, the sample data is in the US West \(Oregon\) Region \(us\-west\-2\), so you need a cluster that is also in us\-west\-2\. If you don't have an Amazon Redshift cluster, you can create a new cluster in us\-west\-2 and install a SQL client by following the steps in [Getting Started with Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html)\. 

If you already have a cluster, your cluster needs to be version 1\.0\.1294 or later to use Amazon Redshift Spectrum\. To find the version number for your cluster, run the following command\.

```
select version();
```

To force your cluster to update to the latest cluster version, adjust your [maintenance window](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-maintenance-windows)\. 

## Steps to Get Started<a name="c-getting-started-using-spectrum-steps"></a>

To get started using Amazon Redshift Spectrum, follow these steps:
+ [Step 1\. Create an IAM Role for Amazon Redshift](c-getting-started-using-spectrum-create-role.md) 
+ [Step 2: Associate the IAM Role with Your Cluster](c-getting-started-using-spectrum-add-role.md) 
+ [Step 3: Create an External Schema and an External Table](c-getting-started-using-spectrum-create-external-table.md) 
+ [Step 4: Query Your Data in Amazon S3](c-getting-started-using-spectrum-query-s3-data.md) 