# Controlling access for cross\-account datashares<a name="control-access"></a>

Following are considerations for controlling cross\-account datashare access\.

**Topics**
+ [Managing datashares at different states](#manage-status)
+ [Managing access to data sharing API operations with IAM policies](#iam-policy)
+ [Managing cluster encryption](#encryption)
+ [Integrating Amazon Redshift data sharing with AWS CloudTrail](cloudtrail.md)

## Managing datashares at different states<a name="manage-status"></a>

With cross\-account datashares, there are different statuses of datashares that require your actions\. Your datashare can have the status are active, action required, or inactive\. 

Following describes each datashare status and its required action:
+ When a producer cluster administrator creates a datashare, the datashare status on the producer cluster is **Pending authorization**\. The producer cluster administrator can authorize data consumers to access the datashare\. There isn't any action for the consumer cluster administrator\.
+ When a producer cluster administrator authorizes the datashare, the datashare status becomes **Authorized** on the producer cluster\. There isn't any action for the producer cluster administrator\. When there is at least one association with a data consumer for the datashare, the datashare status changes from **Authorized** to **Active**\.

  The datashare share status then becomes **Available \(Action required on the Amazon Redshift console\)** on the consumer cluster\. The consumer cluster administrator can associate the datashare with data consumers or reject the datashare\. The consumer cluster administrator can also use the AWS CLI command `describeDatashareforConsumer` to view the status of datashares\. Or the administrator can use the CLI command `describeDatashare` and provide the datashare Amazon Resource Name \(ARN\) to view the status of the datashare\.
+ When the consumer cluster administrator associates a datashare with data consumers, the datashare status becomes **Active** on the producer cluster\. When there is at least one association with a data consumer for the datashare, the datashare status changes from **Authorized** to **Active**\. There isn't any action required for the producer cluster administrator\.

  The datashare status becomes **Active** on the consumer cluster\. There isn't any action required for the consumer cluster administrator\.
+ When the consumer cluster administrator removes a consumer association from a datashare, the datashare status becomes either **Active** or **Authorized**\. It becomes **Active** when there is at least one association exists for the datashare with another data consumer\. It becomes **Authorized** when there isn't any consumer association with the datashare on the producer cluster\. There isn't any action for the producer cluster administrator\.

  The datashare status becomes **Action required** on the consumer cluster if all associations are removed\. The consumer cluster administrator can reassociate a datashare with data consumers when the datashare is available to the consumers\.
+ When a consumer cluster administrator declines a datashare, the datashare status on the producer cluster becomes **Action required** and **Declined** on the consumer cluster\. The producer cluster administrator can reauthorize the datashare\. There isn't any action for the consumer cluster administrator\.
+ When the producer cluster administrator removes authorization from a datashare, the datashare's status becomes **Action required** on the producer cluster\. The producer cluster administrator can choose to reauthorize the datashare, if required\. There isn't any action required for the consumer cluster administrator\.

## Managing access to data sharing API operations with IAM policies<a name="iam-policy"></a>

To control the access to the data sharing API operations, use IAM action\-based policies to control the access to the data sharing API operations\. For information about how to manage IAM policies, see [Managing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html) in the *IAM User Guide*\.

For information on the permissions required to use the data sharing API operations, see [Permissions required to use the data sharing API operations](https://docs.aws.amazon.com/redshift/latest/mgmt/redshift-iam-access-control-identity-based.html) in the *Amazon Redshift Cluster Management Guide*\.

To make cross\-account data sharing more secure, you can use a conditional key `ConsumerIdentifier` for the `AuthorizeDataShare` and `DeauthorizeDataShare` APIs to explicitly control which AWS accounts can make calls to the two APIs\.

You can deny authorizing or deauthorizing data sharing against any consumer that isn't your own account by specifying the AWS account number in the IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Deny",
            "Action": [
                "redshift:AuthorizeDataShare",
                "redshift:DeauthorizeDataShare"
            ],
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "redshift:ConsumerIdentifier": "555555555555"
                }
            }
        }
    ]
}
```

You can allow a producer with a DataShareArn **testshare2** to explicitly share with a consumer with an AWS account of 111122223333 in the IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "redshift:AuthorizeDataShare",
                "redshift:DeauthorizeDataShare"
            ],
            "Resource": "arn:aws:redshift:us-east-1:666666666666:datashare:af06285e-8a45-4ee9-b598-648c218c8ff1/testshare2",
            "Condition": {
                "StringEquals": {
                    "redshift:ConsumerIdentifier": "111122223333"
                }
            }
        }
    ]
}
```

## Managing cluster encryption<a name="encryption"></a>

To share data across AWS account, both the producer and consumer clusters must be encrypted\.

In Amazon Redshift, you can enable database encryption for your clusters to help protect data at rest\. When you enable encryption for a cluster, the data blocks and system metadata are encrypted for the cluster and its snapshots\. You can enable encryption when you launch your cluster, or you can modify an unencrypted cluster to use AWS Key Management Service \(AWS KMS\) encryption\. For more information about Amazon Redshift database encryption, see [Amazon Redshift database encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) in the *Amazon Redshift Cluster Management Guide*\.