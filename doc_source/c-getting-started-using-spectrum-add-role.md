# Step 2: Associate the IAM role with your cluster<a name="c-getting-started-using-spectrum-add-role"></a>

Now you have an IAM role that authorizes Amazon Redshift to access the external Data Catalog and Amazon S3 for you\. At this point, you must associate that role with your Amazon Redshift cluster\.

**To associate an IAM role with a cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Clusters**, then choose the name of the cluster that you want to update\. 

1. For **Actions**, choose **Manage IAM roles**\. The **IAM roles** page appears\. 

1. Either choose **Enter ARN** and then enter an ARN or an IAM role, or choose an IAM role from the list\. Then choose **Add IAM role** to add it to the list of **Attached IAM roles**\. 

1. Choose **Done** to associate the IAM role with the cluster\. The cluster is modified to complete the change\. 