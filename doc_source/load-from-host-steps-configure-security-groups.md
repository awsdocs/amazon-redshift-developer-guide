# Step 3: Configure the Host to Accept All of the Amazon Redshift Cluster's IP Addresses<a name="load-from-host-steps-configure-security-groups"></a>

 If you are working with an Amazon EC2 instance or an Amazon EMR cluster, add Inbound rules to the host's security group to allow traffic from each Amazon Redshift cluster node\. For **Type**, select SSH with TCP protocol on Port 22\. For **Source**, enter the Amazon Redshift cluster node IP addresses you retrieved in [Step 1: Retrieve the Cluster Public Key and Cluster Node IP Addresses](load-from-host-steps-retrieve-key-and-ips.md)\. For information about adding rules to an Amazon EC2 security group, see [Authorizing Inbound Traffic for Your Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/authorizing-access-to-an-instance.html) in the *Amazon EC2 User Guide*\. 

Use the Private IP addresses when: 
+ You have an Amazon Redshift cluster that is not in a Virtual Private Cloud \(VPC\), and an Amazon EC2 \-Classic instance, both of which are in the same AWS region\. 
+  You have an Amazon Redshift cluster that is in a VPC, and an Amazon EC2 \-VPC instance, both of which are in the same AWS region and in the same VPC\. 

 Otherwise, use the Public IP addresses\.

For more information about using Amazon Redshift in a VPC, see [Managing Clusters in Virtual Private Cloud \(VPC\)](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-vpc.html) in the *Amazon Redshift Cluster Management Guide*\. 