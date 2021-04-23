# Data sharing considerations<a name="considerations"></a>

Following are considerations for working with Amazon Redshift data sharing:
+ Amazon Redshift only supports data sharing on the ra3\.16xlarge, ra3\.4xlarge, and ra3\.xlplus instance types for producer and consumer clusters\.
+ Amazon Redshift supports data sharing for clusters with homogeneous encryption configuration\. In other words, you can share data among two or more encrypted Amazon Redshift clusters\. Or you can share data among two or more unencrypted Amazon Redshift clusters for clusters that are within the same AWS account\. When sharing data between encrypted clusters, you can use different encryption keys for each cluster\. 
+ Use datashares only when you are sharing data between different Amazon Redshift clusters\. Within the same cluster, read data from other databases using the simple three\-part notation database\.schema\.table\. Make sure that you have the necessary permissions to perform queries that span multiple databases\.
+ Amazon Redshift associates one datashare with a single Amazon Redshift database\. You can only add objects from that database to the associated datashare\.
+ You can create multiple database shares on the same Amazon Redshift database\. Use unique names for datashares created within the Amazon Redshift cluster\.
+ You can create multiple schemas pointing to databases created from the datashare in the consumer cluster\.
+ You can share data in your Amazon Redshift clusters that are in the same AWS account\.
+ You can share data in your Amazon Redshift clusters that are across Availability Zones\.
+ As a datashare user, you continue to connect to your local cluster database only\. You can't connect to the databases created from a datashare but can read from those databases\.
+ With data sharing, producer cluster is charged for the data they shared and consumer clusters get charged for the compute they use in accessing the shared data\.
+ You can't use data sharing for clusters across different AWS Regions\. You can only use cross\-Region snapshot copy to share data across Regions\.
+ The performance of the queries on shared data depends on the compute capacity of the consumer clusters\.