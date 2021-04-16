# Creating external schemas for Amazon Redshift Spectrum<a name="c-spectrum-external-schemas"></a>

All external tables must be created in an external schema, which you create using a [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md) statement\. 

**Note**  
Some applications use the term *database* and *schema* interchangeably\. In Amazon Redshift, we use the term *schema*\. 

An Amazon Redshift external schema references an external database in an external data catalog\. You can create the external database in Amazon Redshift, in [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/catalog.html), in [AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/components-overview.html#data-catalog-intro), or in an Apache Hive metastore, such as [Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-what-is-emr.html)\. If you create an external database in Amazon Redshift, the database resides in the Athena Data Catalog\. To create a database in a Hive metastore, you need to create the database in your Hive application\.

Amazon Redshift needs authorization to access the Data Catalog in Athena and the data files in Amazon S3 on your behalf\. To provide that authorization, you first create an AWS Identity and Access Management \(IAM\) role\. Then you attach the role to your cluster and provide Amazon Resource Name \(ARN\) for the role in the Amazon Redshift `CREATE EXTERNAL SCHEMA` statement\. For more information about authorization, see [IAM policies for Amazon Redshift Spectrum](c-spectrum-iam-policies.md)\.

**Note**  
If you currently have Redshift Spectrum external tables in the Athena Data Catalog, you can migrate your Athena Data Catalog to an AWS Glue Data Catalog\. To use an AWS Glue Data Catalog with Redshift Spectrum, you might need to change your IAM policies\. For more information, see [Upgrading to the AWS Glue Data Catalog](https://docs.aws.amazon.com/athena/latest/ug/glue-athena.html#glue-upgrade) in the *Amazon Athena User Guide*\.

To create an external database at the same time you create an external schema, specify `FROM DATA CATALOG` and include the `CREATE EXTERNAL DATABASE` clause in your `CREATE EXTERNAL SCHEMA` statement\. 

The following example creates an external schema named `spectrum_schema` using the external database `spectrum_db`\.

```
create external schema spectrum_schema from data catalog 
database 'spectrum_db' 
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole'
create external database if not exists;
```

If you manage your data catalog using Athena, specify the Athena database name and the AWS Region in which the Athena Data Catalog is located\. 

The following example creates an external schema using the default `sampledb` database in the Athena Data Catalog\.

```
create external schema athena_schema from data catalog 
database 'sampledb' 
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole' 
region 'us-east-2';
```

**Note**  
The `region` parameter references the AWS Region in which the Athena Data Catalog is located, not the location of the data files in Amazon S3\.

If you manage your data catalog using a Hive metastore, such as Amazon EMR, your security groups must be configured to allow traffic between the clusters\. 

In the CREATE EXTERNAL SCHEMA statement, specify `FROM HIVE METASTORE` and include the metastore's URI and port number\. The following example creates an external schema using a Hive metastore database named `hive_db`\.

```
create external schema hive_schema
from hive metastore
database 'hive_db'
uri '172.10.10.10' port 99
iam_role 'arn:aws:iam::123456789012:role/MySpectrumRole'
```

To view external schemas for your cluster, query the PG\_EXTERNAL\_SCHEMA catalog table or the SVV\_EXTERNAL\_SCHEMAS view\. The following example queries SVV\_EXTERNAL\_SCHEMAS, which joins PG\_EXTERNAL\_SCHEMA and PG\_NAMESPACE\.

```
select * from svv_external_schemas
```

For the full command syntax and examples, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md)\.

## Working with external catalogs in Amazon Redshift Spectrum<a name="c-spectrum-external-catalogs"></a>

The metadata for Amazon Redshift Spectrum external databases and external tables is stored in an external data catalog\. By default, Redshift Spectrum metadata is stored in an Athena Data Catalog\. You can view and manage Redshift Spectrum databases and tables in your Athena console\. 

You can also create and manage external databases and external tables using Hive data definition language \(DDL\) using Athena or a Hive metastore, such as Amazon EMR\. 

**Note**  
We recommend using Amazon Redshift to create and manage external databases and external tables in Redshift Spectrum\.

### Viewing Redshift Spectrum databases in Athena<a name="c-spectrum-athena-external-catalogs"></a>

You can create an external database by including the CREATE EXTERNAL DATABASE IF NOT EXISTS clause as part of your CREATE EXTERNAL SCHEMA statement\. In such cases, the external database metadata is stored in your Athena data catalog\. The metadata for external tables that you create qualified by the external schema is also stored in your Athena Data Catalog\. 

Athena maintains a Data Catalog for each supported AWS Region\. To view table metadata, log on to the Athena console and choose **Catalog Manager**\. The following example shows the Athena Catalog Manager for the US West \(Oregon\) Region\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-athena-catalog.png)

If you create and manage your external tables using Athena, register the database using CREATE EXTERNAL SCHEMA\. For example, the following command registers the Athena database named `sampledb`\.

```
create external schema athena_sample
from data catalog
database 'sampledb'
iam_role 'arn:aws:iam::123456789012:role/mySpectrumRole'
region 'us-east-1';
```

When you query the SVV\_EXTERNAL\_TABLES system view, you see tables in the Athena `sampledb` database and also tables that you created in Amazon Redshift\.

```
select * from svv_external_tables;
```

```
schemaname    | tablename        | location                                               
--------------+------------------+--------------------------------------------------------
athena_sample | elb_logs         | s3://athena-examples/elb/plaintext           
athena_sample | lineitem_1t_csv  | s3://myspectrum/tpch/1000/lineitem_csv                
athena_sample | lineitem_1t_part | s3://myspectrum/tpch/1000/lineitem_partition          
spectrum      | sales            | s3://awssampledbuswest2/tickit/spectrum/sales          
spectrum      | sales_part       | s3://awssampledbuswest2/tickit/spectrum/sales_part
```

### Registering an Apache Hive metastore database<a name="c-spectrum-hive-metastore"></a>

If you create external tables in an Apache Hive metastore, you can use CREATE EXTERNAL SCHEMA to register those tables in Redshift Spectrum\. 

In the CREATE EXTERNAL SCHEMA statement, specify the FROM HIVE METASTORE clause and provide the Hive metastore URI and port number\. The IAM role must include permission to access Amazon S3 but doesn't need any Athena permissions\. The following example registers a Hive metastore\. 

```
create external schema if not exists hive_schema
from hive metastore
database 'hive_database'
uri 'ip-10-0-111-111.us-west-2.compute.internal' port 9083 
iam_role 'arn:aws:iam::123456789012:role/mySpectrumRole';
```

### Enabling your Amazon Redshift cluster to access your Amazon EMR cluster<a name="c-spectrum-enabling-emr-access"></a>

If your Hive metastore is in Amazon EMR, you must give your Amazon Redshift cluster access to your Amazon EMR cluster\. To do so, you create an Amazon EC2 security group\. You then allow all inbound traffic to the EC2 security group from your Amazon Redshift cluster's security group and your Amazon EMR cluster's security group\. Then you add the EC2 security to both your Amazon Redshift cluster and your Amazon EMR cluster\.

**To enable your Amazon Redshift cluster to access your Amazon EMR cluster**

1. In Amazon Redshift, make a note of your cluster's security group name\. 
**Note**  
A new console is available for Amazon Redshift\. Choose either the **New console** or the **Original console** instructions based on the console that you are using\. The **New console** instructions are open by default\.

   **New console**

   To display the security group, do the following:

   1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

   1. On the navigation menu, choose **CLUSTERS**, then choose the cluster from the list to open its details\. Choose **Properties** and view the **Network and security** section\. 

   1. Find your security group in **VPC security group**\. 

   **Original console**

   In the Amazon Redshift console, choose your cluster\. Find your cluster security groups in the **Cluster Properties** group\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-redshift-security-groups.png)

1. In Amazon EMR, make a note of the EMR master node security group name\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-emr-security-groups.png)

1. Create or modify an Amazon EC2 security group to allow connection between Amazon Redshift and Amazon EMR:

   1. In the Amazon EC2 dashboard, choose **Security Groups**\. 

   1. Choose **Create Security Group**\. 

   1. If using VPC, choose the VPC that both your Amazon Redshift and Amazon EMR clusters are in\. 

   1. Add an inbound rule\. 

   1. For **Type**, choose **TCP**\. 

   1. For **Source**, choose **Custom**\. 

   1. Enter the name of your Amazon Redshift security group\. 

   1. Add another inbound rule\. 

   1. For **Type**, choose **TCP**\. 

   1. For **Port Range**, enter **9083**\.
**Note**  
The default port for an EMR HMS is 9083\. If your HMS uses a different port, specify that port in the inbound rule and in the external schema definition\. 

   1. For **Source**, choose **Custom**\. 

   1. Enter the name of your Amazon EMR security group\. 

   1. Choose **Create**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-ec2-create-security-group.png)

1. Add the Amazon EC2 security group you created in the previous step to your Amazon Redshift cluster and to your Amazon EMR cluster:

   1. In Amazon Redshift, choose your cluster\. 

   1. Choose **Cluster**, **Modify**\. 

   1. In **VPC Security Groups**, add the new security group by pressing CRTL and choosing the new security group name\. 

   1. In Amazon EMR, choose your cluster\. 

   1. Under **Hardware**, choose the link for the Master node\. 

   1. Choose the link in the **EC2 Instance ID** column\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-emr-add-security-group.png)

   1. For **Actions**, choose **Networking**, **Change Security Groups**\. 

   1. Choose the new security group\. 

   1. Choose **Assign Security Groups**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/spectrum-emr-assign-security-group.png)