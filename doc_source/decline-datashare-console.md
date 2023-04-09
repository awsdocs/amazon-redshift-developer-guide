# Declining datashares<a name="decline-datashare-console"></a>

As a consumer cluster administrator, you can reject any datashare whose state is [available or active](https://docs.aws.amazon.com/redshift/latest/dg/access-cross-account.html#manage-status)\. After you reject a datashare, consumer cluster users lose access to the datashare\. Amazon Redshift doesn't return the rejected datashare if you call the `DescribeDataSharesForConsumer` API operation\. If the producer cluster administrator runs the `DescribeDataSharesForProducer` API operation, they will see that the datashare was rejected\. Once a datashare is rejected, the producer cluster administrator can authorize the datashare to a consumer cluster again, and the consumer cluster administrator can choose to associate their AWS account with the datashare or reject it\. 

If your AWS account has an association to a datashare and a pending association to a datashare that's managed by Lake Formation, rejecting the datashare association that's managed by Lake Formation also rejects the original datashare\. To reject a specific association, the producer cluster administrator can remove authorization from a specified datashare\. This action doesn't affect other datashares\.

To reject a datashare, use the AWS console, the API operation `RejectDataShare`, or `reject-datashare` in the AWS CLI\.

**To reject a datashare using the AWS console:**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. In the navigation menu, choose **Datashares**\.

1. Choose **From other accounts**\.

1. In the **Datashares from other accounts** section, choose the datashare you want to decline\. When the **Decline datashare** page appears, choose **Decline**\.

After you decline the datashares, you can't revert the change\. Amazon Redshift removes the datashares from the list\. To see the datashare again, the producer administrator must authorize it again\.