# Step 2: Associate the IAM Role with Your Cluster<a name="c-getting-started-using-spectrum-add-role"></a>

<<<<<<< HEAD
After you have created an IAM role that authorizes Amazon Redshift to access the external data catalog and Amazon S3 on your behalf, you must associate that role with your Amazon Redshift cluster\.<a name="spectrum-get-started-add-role"></a>
=======
After you have created an IAM role that authorizes Amazon Redshift to access the external data catalog and Amazon S3 on your behalf, you must associate that role with your Amazon Redshift cluster\.
>>>>>>> d940ef9046cd1aeb28a5d74442d2035df797200d

**To associate the IAM role with your cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation pane, choose **Clusters**\.

1. In the list, choose the cluster that you want to manage IAM role associations for\.

1. Choose **Manage IAM Roles**\.

1. Select your IAM role from the **Available roles** list\. 

1. Choose **Apply Changes** to update the IAM roles that are associated with the cluster\.