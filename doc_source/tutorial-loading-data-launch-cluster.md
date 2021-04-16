# Step 1: Create a cluster<a name="tutorial-loading-data-launch-cluster"></a>

If you already have a cluster that you want to use, you can skip this step\. 

For the exercises in this tutorial, use a four\-node cluster\. 

## New console<a name="tutorial-loading-data-newconsole"></a>

**To create a cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
**Important**  
If you use IAM user credentials, make sure that you have the necessary permissions to perform the cluster operations\. For more information, see [Controlling access to IAM users](https://docs.aws.amazon.com/redshift/latest/mgmt/iam-redshift-user-mgmt.html) in the *Amazon Redshift Cluster Management Guide*\.

1. At top right, choose the AWS Region in which you want to create the cluster\. For the purposes of this tutorial, choose **US West \(Oregon\)**\.

1. On the navigation menu, choose **CLUSTERS**, then choose **Create cluster**\. The **Create cluster** page appears\. 

1. Choose **dc2\.large** for the node type in the **Compute optimized** section\. Then choose **4** for the **Nodes**\. 

1. In the **Cluster details** section, specify values for **Cluster identifier**, **Database port**, **Master user name**, and **Master user password**\. 

1. In the **Cluster permissions** section, choose an IAM role from **Available IAM roles**\. This role should be one that you previously created and that has access to Amazon S3\. Then choose **Add IAM role** to add it to the list of **Attached IAM roles** for the cluster\.

1. Choose **Create cluster**\. 

## Original console<a name="tutorial-loading-data-originalconsole"></a>

Follow the steps in [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/), but choose **Multi Node** for **Cluster Type** and set **Number of Compute Nodes** to **4**\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tutorial-optimize-tables-console-cluster-type.png)

Follow the [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/) steps to connect to your cluster from a SQL client and test a connection\. You don't need to complete the remaining Getting Started steps to create tables, upload data, and try example queries\. 

## Next step<a name="tutorial-loading-next-step2"></a>

[Step 2: Download the data files](tutorial-loading-data-download-files.md)