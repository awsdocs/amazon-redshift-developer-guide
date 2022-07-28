# Sharing data across AWS accounts<a name="across-account"></a>

You can share data for read purposes across AWS accounts\. Sharing data across AWS accounts works similarly to sharing data within an account\. The difference is that there is a two\-way handshake required in sharing data across AWS accounts\. A producer account administrators can either authorize consumer accounts to access datashares or choose not to authorize any access\. To use an authorized datashare, a consumer account administrator can associate the datashare\. The administrator can associate the datashare with an entire AWS account or with specific clusters in the consumer account, or decline the datashare\. For more information about sharing data within an account, see [Sharing data within an AWS account](within-account.md)\.

A datashare can have data consumers that are either cluster namespaces in the same account or different AWS accounts\. You don't need to create separate datashares for sharing within an account and cross\-account sharing\.

For cross\-account data sharing, both the producer and consumer cluster must be encrypted\.

When sharing data with AWS accounts, producer cluster administrators share with the AWS account as an entity\. A consumer cluster administrator can decide which cluster namespaces in the consumer account get access to a datashare\.

**Topics**
+ [Producer cluster administrator actions](producer-cluster-admin.md)
+ [Consumer account administrator actions](consumer-account-admin.md)
+ [Consumer cluster administrator actions](consumer-cluster-admin.md)