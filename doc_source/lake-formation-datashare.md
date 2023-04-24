# AWS Lake Formation\-managed Redshift datashares<a name="lake-formation-datashare"></a>

 Using AWS Lake Formation, you can centrally define and enforce database, table, column, and row\-level access permissions of Amazon Redshift datashares and restrict user access to objects within a datashare\. By sharing data through Lake Formation, you can define permissions in Lake Formation and apply those permissions to any datashare and its objects\. For example, if you have a table containing employee information, you can use Lake Formation's column\-level filters to prevent employees who don't work in the HR department from seeing personally identifiable information \(PII\), such as a social security number\. For more information about data filters, see [Data filtering and cell\-level security in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering.html) in the *AWS Lake Formation Developer Guide*\. 

You can also use tags in Lake Formation to configure permissions on Lake Formation resources\. For more information, see [Lake Formation Tag\-based access control](https://docs.aws.amazon.com/lake-formation/latest/dg/tag-based-access-control.html)\.

 Amazon Redshift currently supports data sharing via Lake Formation when sharing within the same account or across accounts\. Cross\-Region sharing is currently not supported\. 

The following is a high\-level overview of how to use Lake Formation to control datashare permissions:

1. In Amazon Redshift, the producer account administrator creates a datashare on the producer cluster and grants usage to a Lake Formation account\.

1. The producer cluster administrator authorizes the Lake Formation account to access the datashare\.

1. The Lake Formation administrator discovers and registers the datashares\. They must also discover the AWS Glue ARNs they have access to and associate the datashares with an AWS Glue Data Catalog ARN\. If you're using the AWS CLI you can discover and accept datashares with the Redshift CLI operations `describe-data-shares` and `associate-data-share-consumer`\. To register a datashare, use the Lake Formation CLI operation `register-resource`\.

1. The Lake Formation administrator creates a federated database in the AWS Glue Data Catalog, and configures Lake Formation permissions to control user access to objects within the datashare\.  For more information about federated databases in AWS Glue, see [Managing permissions for data in an Amazon Redshift datashare](https://docs.aws.amazon.com/lake-formation/latest/dg/data-sharing-redshift.html)\.

1. The Lake Formation administrator discovers the AWS Glue databases they have access to and associates the datashare with an AWS Glue Data Catalog ARN\.

1. The Redshift administrator discovers the AWS Glue database ARNs they have access to, creates an external database in the Amazon Redshift consumer cluster using a AWS Glue database ARN, and grants usage to [database users authenticated with IAM credentials](https://docs.aws.amazon.com/redshift/latest/mgmt/options-for-providing-iam-credentials.html) to start querying the Amazon Redshift database\.

1. Database users can use the views SVV\_EXTERNAL\_TABLES and SVV\_EXTERNAL\_COLUMNS to find all of the tables or columns within the AWS Glue database that they have access to, and then they can query the AWS Glue database’s tables\.

1. When the producer cluster administrator decides to no longer share the data with the consumer cluster, the producer cluster administrator can revoke usage, deauthorize, or delete the datashare from Redshift\. The associated permissions and objects in Lake Formation are not automatically deleted\.

For more information about sharing a datashare with AWS Lake Formation as a producer cluster administrator, see [Working with Lake Formation\-managed datashares as a producer](lake-formation-getting-started-producer.md)\. To consume the shared data from the producer cluster, see [Working with Lake Formation\-managed datashares as a consumer](lake-formation-getting-started-consumer.md)\.

## Considerations and limitations when using AWS Lake Formation with Amazon Redshift<a name="lake-formation-datashare-considerations"></a>

The following are considerations and limitations for sharing Amazon Redshift data via Lake Formation\. For information on data sharing considerations and limitations, see [Considerations when using data sharing in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/dg/considerations.html)\. For information about Lake Formation limitations, see [Notes on working with Amazon Redshift datashares in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/notes-rs-datashare.html)\.
+ Sharing a datashare to Lake Formation across Regions is currently unsupported\.
+ If column\-level filters are defined for a user on a shared relation, performing a `SELECT *` operation returns only the columns the user has access to\.
+ Cell\-level filters from Lake Formation are unsupported\.
+ If you created and shared a view and its tables to Lake Formation, you can configure filters to manage access of the tables, Amazon Redshift enforces Lake Formation defined policies when consumer cluster users access shared objects\. When a user accesses a view shared with Lake Formation, Redshift enforces only the Lake Formation policies defined on the view and not the tables contained within the view\. However, when users directly access the table, Redshift enforces the defined Lake Formation policies on the table\.
+ You can't create materialized views based on a table if the table has Lake Formation filters configured\.
+ The Lake Formation administrator must have [ data lake administrator](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#create-data-lake-admin) permissions and the [ required permissions to accept a datashare](https://docs.aws.amazon.com/lake-formation/latest/dg/redshift-ds-prereqs.html)\.
+ The producer consumer cluster must be an RA3 cluster with the latest Amazon Redshift cluster version or a serverless workgroup to share datashares via Lake Formation\.
+ Both the producer and consumer clusters must be encrypted\.
+ Redshift row\-level and column\-level access control policies implemented in the producer cluster are ignored when the datashare is shared to Lake Formation\. The Lake Formation administrator must configure these policies in Lake Formation\. The producer cluster administrator can turn off RLS for a table by using the [ALTER TABLE](https://docs.aws.amazon.com/redshift/latest/dg/r_ALTER_TABLE.html) command\.
+ Sharing datashares via Lake Formation is only available to users who have access to both Redshift and Lake Formation\.