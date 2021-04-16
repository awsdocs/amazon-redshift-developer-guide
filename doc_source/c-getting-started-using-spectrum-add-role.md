# Step 2: Associate the IAM role with your cluster<a name="c-getting-started-using-spectrum-add-role"></a>

Now you have an IAM role that authorizes Amazon Redshift to access the external Data Catalog and Amazon S3 for you\. At this point, you must associate that role with your Amazon Redshift cluster\.

**Note**  
A new console is available for Amazon Redshift\. Choose either the **New console** or the **Original console** instructions based on the console that you are using\. The **New console** instructions are open by default\.

## New console<a name="spectrum-iam-role"></a>

**To associate an IAM role with a cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **CLUSTERS**, then choose the name of the cluster that you want to update\. 

1. For **Actions**, choose **Manage IAM roles**\. The **IAM roles** page appears\. 

1. Either Choose **Enter ARN** and then enter an ARN or an IAM role, or choose an IAM role from the list\. Then choose **Add IAM role** to add it to the list of **Attached IAM roles**\. 

1. Choose **Done** to associate the IAM role with the cluster\. The cluster is modified to complete the change\. 

## Original console<a name="spectrum-iam-role-originalconsole"></a><a name="spectrum-get-started-add-role"></a>

**To associate the IAM role with your cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Clusters**\.

1. In the list, choose the cluster that you want to manage IAM role associations for\.

1. Choose **Manage IAM Roles**\.

1. For **Available roles**, choose your IAM role\. 

1. Choose **Apply Changes** to update the IAM roles that are associated with the cluster\.