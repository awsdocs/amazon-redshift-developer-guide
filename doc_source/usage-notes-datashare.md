# Considerations for data sharing<a name="usage-notes-datashare"></a>

Consider the following when you use Amazon Redshift data sharing:
+ Use data shares only when you are sharing data between different Amazon Redshift clusters\. Within the same cluster, read data from other databases using a simple three\-part notation database\.schema\.table\. You must have the necessary permissions to perform queries that span multiple databases\.
+ Amazon Redshift associates one data share with a single Amazon Redshift database\. You can only add objects from that database to the associated data share\.
+ You can create multiple database shares on the same Amazon Redshift database\. Use unique names for data shares created within the Amazon Redshift cluster\.
+ You can create multiple schemas pointing to databases created from the data share in the consumer cluster\.
+ You can share data in your Amazon Redshift clusters that are in the same AWS account or across Availability Zones\.
+ As a data share user, you continue to connect to your local cluster database only\. You can't connect to the databases created from a data share but can read from those databases\.
+ With data sharing, producer cluster is charged for the data they shared and consumer clusters get charged for the compute they use in accessing the shared data\.
+ You can't use data sharing for clusters across different AWS Regions\. You can only use cross\-Region snapshot copy to share data across Regions\.
+ The performance of the queries depends on the compute capacity of the consumer clusters\.