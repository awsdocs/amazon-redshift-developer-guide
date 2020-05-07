# Step 2: Create an Amazon EMR cluster<a name="load-from-emr-steps-create-cluster"></a>

The COPY command loads data from files on the Amazon EMR Hadoop Distributed File System \(HDFS\)\. When you create the Amazon EMR cluster, configure the cluster to output data files to the cluster's HDFS\.

**To create an Amazon EMR cluster**

1. Create an Amazon EMR cluster in the same AWS Region as the Amazon Redshift cluster\. 

   If the Amazon Redshift cluster is in a VPC, the Amazon EMR cluster must be in the same VPC group\. If the Amazon Redshift cluster uses EC2\-Classic mode \(that is, it is not in a VPC\), the Amazon EMR cluster must also use EC2\-Classic mode\. For more information, see [Managing Clusters in Virtual Private Cloud \(VPC\)](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-vpc.html) in the *Amazon Redshift Cluster Management Guide*\.

1. Configure the cluster to output data files to the cluster's HDFS\. The HDFS file names must not include asterisks \(\*\) or question marks \(?\)\.
**Important**  
The file names must not include asterisks \( \* \) or question marks \( ? \)\.

1. Specify **No** for the **Auto\-terminate** option in the Amazon EMR cluster configuration so that the cluster remains available while the COPY command executes\. 
**Important**  
If any of the data files are changed or deleted before the COPY completes, you might have unexpected results, or the COPY operation might fail\. 

1. Note the cluster ID and the master public DNS \(the endpoint for the Amazon EC2 instance that hosts the cluster\)\. You will use that information in later steps\. 