# Loading Data from Amazon EMR<a name="loading-data-from-emr"></a>

You can use the COPY command to load data in parallel from an Amazon EMR cluster configured to write text files to the cluster's Hadoop Distributed File System \(HDFS\) in the form of fixed\-width files, character\-delimited files, CSV files, or JSON\-formatted files\.

## Loading Data From Amazon EMR Process<a name="load-from-emr-process"></a>

This section walks you through the process of loading data from an Amazon EMR cluster\. The following sections provide the details you need to accomplish each step\.
+ **[Step 1: Configure IAM Permissions](load-from-emr-steps-configure-iam.md)**

  The users that create the Amazon EMR cluster and run the Amazon Redshift COPY command must have the necessary permissions\.
+ **[Step 2: Create an Amazon EMR Cluster](load-from-emr-steps-create-cluster.md)**

  Configure the cluster to output text files to the Hadoop Distributed File System \(HDFS\)\. You will need the Amazon EMR cluster ID and the cluster's master public DNS \(the endpoint for the Amazon EC2 instance that hosts the cluster\)\. 
+ **[Step 3: Retrieve the Amazon Redshift Cluster Public Key and Cluster Node IP Addresses](load-from-emr-steps-retrieve-key-and-ips.md)**

  The public key enables the Amazon Redshift cluster nodes to establish SSH connections to the hosts\. You will use the IP address for each cluster node to configure the host security groups to permit access from your Amazon Redshift cluster using these IP addresses\. 
+ **[Step 4: Add the Amazon Redshift Cluster Public Key to Each Amazon EC2 Host's Authorized Keys File](load-from-emr-steps-add-key-to-host.md)** 

  You add the Amazon Redshift cluster public key to the host's authorized keys file so that the host will recognize the Amazon Redshift cluster and accept the SSH connection\. 
+ **[Step 5: Configure the Hosts to Accept All of the Amazon Redshift Cluster's IP Addresses](load-from-emr-steps-configure-security-groups.md)** 

  Modify the Amazon EMR instance's security groups to add ingress rules to accept the Amazon Redshift IP addresses\.
+ **[Step 6: Run the COPY Command to Load the Data](load-from-emr-steps-run-copy.md)**

  From an Amazon Redshift database, run the COPY command to load the data into an Amazon Redshift table\. 