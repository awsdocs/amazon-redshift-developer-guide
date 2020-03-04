# Using Redshift Spectrum with AWS Lake Formation<a name="spectrum-lake-formation"></a>

You can use AWS Lake Formation to centrally define and enforce database, table, and column\-level access policies to data stored in Amazon S3\. After your data is registered with an AWS Glue Data Catalog enabled with Lake Formation, you can query it by using several services, including Redshift Spectrum\. 

Lake Formation provides the security and governance of the Data Catalog\. Within Lake Formation, you can grant and revoke permissions to the Data Catalog objects, such as databases, tables, columns, and underlying Amazon S3 storage\.

**Important**  
You can only use Redshift Spectrum with a Lake Formation enabled Data Catalog in AWS Regions where Lake Formation is available\. For a list of available Regions, see [AWS Lake Formation Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/lake-formation.html) in the AWS General Reference\. 

By using Redshift Spectrum with Lake Formation, you can do the following:
+ Use Lake Formation as a centralized place where you grant and revoke permissions and access control policies on all of your data in the data lake\. Lake Formation provides a hierarchy of permissions to control access to databases and tables in a Data Catalog\. For more information, see [Lake Formation Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/lake-formation-permissions.html)\. 
+ Create external tables and run queries on data in the data lake\. Before users in your account can run queries, a data lake account administrator registers your existing Amazon S3 paths containing source data with Lake Formation\. The administrator also creates tables and grants permissions to your users\. Access can be granted on databases, tables, or columns\. 

  After the data is registered in the Data Catalog, each time users try to run queries, Lake Formation verifies access to the table for that specific principal\. Lake Formation vends temporary credentials to Redshift Spectrum, and the query runs\.  

When you use Redshift Spectrum with a Data Catalog enabled for Lake Formation, an IAM role associated with the cluster must have permission to the Data Catalog\. 

**Important**  
You can't chain IAM roles when using Redshift Spectrum with a Data Catalog enabled for Lake Formation\.

To learn more about the steps required to set up AWS Lake Formation to use with Redshift Spectrum, see [Tutorial: Creating a Data Lake from a JDBC Source in Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-tutorial.html) in the AWS Lake Formation Developer Guide\. Specifically, see [Query the Data in the Data Lake Using Amazon Redshift Spectrum](https://docs.aws.amazon.com/lake-formation/latest/dg/tut-query-redshift.html) for details about integration with Redshift Spectrum\. The data and AWS resources used in this topic depend on prior steps in the tutorial\.