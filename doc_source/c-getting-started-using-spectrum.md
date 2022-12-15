# Getting started with Amazon Redshift Spectrum<a name="c-getting-started-using-spectrum"></a>

In this tutorial, you learn how to use Amazon Redshift Spectrum to query data directly from files on Amazon S3\. If you already have a cluster and a SQL client, you can complete this tutorial with minimal setup\. 

**Note**  
Redshift Spectrum queries incur additional charges\. The cost of running the sample queries in this tutorial is nominal\. For more information about pricing, see [ Amazon Redshift Spectrum pricing](https://aws.amazon.com/redshift/pricing/#redshift-spectrum-pricing)\.

## Prerequisites<a name="c-getting-started-using-spectrum-prerequisites"></a>

To use Redshift Spectrum, you need an Amazon Redshift cluster and a SQL client that's connected to your cluster so that you can run SQL commands\. The cluster and the data files in Amazon S3 must be in the same AWS Region\. 

In some of the examples that follow, the sample data is in the US East \(N\. Virginia\) Region \(`us-east-1`\), so you need a cluster that is also in `us-east-1`\. Or, you can use Amazon S3 to copy data objects from the following buckets and folders to your bucket in the AWS Region where your cluster is located: 
+ `s3://redshift-downloads/tickit/spectrum/customers/*`
+ `s3://redshift-downloads/tickit/spectrum/sales_partition/*`
+ `s3://redshift-downloads/tickit/spectrum/sales/*`
+ `s3://redshift-downloads/tickit/spectrum/salesevent/*`

Run an Amazon S3 command similar to the following to copy sample data that is located in the US East \(N\. Virginia\) to your AWS Region\. Before running the command create your bucket and folders in your bucket to match your Amazon S3 copy command\. The output of the Amazon S3 copy command confirms that the files are copied to the *bucket\-name* in your desired AWS Region\.

```
aws s3 cp s3://redshift-downloads/tickit/spectrum/ s3://bucket-name/tickit/spectrum/ --copy-props none --recursive
```

## Getting started with Redshift Spectrum using AWS CloudFormation<a name="c-getting-started-using-spectrum-cfn"></a>

As an alternative to the following steps, you can access the Redshift Spectrum DataLake AWS CloudFormation template to create a stack with an Amazon S3 bucket that you can query\. For more information, see [Launch your AWS CloudFormation stack and then query your data in Amazon S3](c-getting-started-using-spectrum-query-s3-data-cfn.md)\.

## Getting started with Redshift Spectrum step by step<a name="c-getting-started-using-spectrum-steps"></a>

To get started using Amazon Redshift Spectrum, follow these steps:
+ [Step 1\. Create an IAM role for Amazon Redshift](c-getting-started-using-spectrum-create-role.md) 
+ [Step 2: Associate the IAM role with your cluster](c-getting-started-using-spectrum-add-role.md) 
+ [Step 3: Create an external schema and an external table](c-getting-started-using-spectrum-create-external-table.md) 
+ [Step 4: Query your data in Amazon S3](c-getting-started-using-spectrum-query-s3-data.md) 