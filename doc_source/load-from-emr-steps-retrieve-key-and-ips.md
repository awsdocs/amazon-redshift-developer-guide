# Step 3: Retrieve the Amazon Redshift cluster public key and cluster node IP addresses<a name="load-from-emr-steps-retrieve-key-and-ips"></a>

**To retrieve the Amazon Redshift cluster public key and cluster node IP addresses for your cluster using the console**

1. Access the Amazon Redshift Management Console\. 

1. Click the **Clusters** link in the navigation pane\. 

1. Select your cluster from the list\. 

1. Locate the **SSH Ingestion Settings** group\. 

   Note the **Cluster Public Key** and **Node IP addresses**\. You will use them in later steps\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/copy-from-ssh-console-2.png)

   You will use the Private IP addresses in Step 3 to configure the Amazon EC2 host to accept the connection from Amazon Redshift\. 

To retrieve the cluster public key and cluster node IP addresses for your cluster using the Amazon Redshift CLI, execute the describe\-clusters command\. For example:

```
aws redshift describe-clusters --cluster-identifier <cluster-identifier> 
```

The response will include a ClusterPublicKey value and the list of private and public IP addresses, similar to the following:

```
{
    "Clusters": [
        {
            "VpcSecurityGroups": [], 
            "ClusterStatus": "available", 
            "ClusterNodes": [
                {
                    "PrivateIPAddress": "10.nnn.nnn.nnn", 
                    "NodeRole": "LEADER", 
                    "PublicIPAddress": "10.nnn.nnn.nnn"
                }, 
                {
                    "PrivateIPAddress": "10.nnn.nnn.nnn", 
                    "NodeRole": "COMPUTE-0", 
                    "PublicIPAddress": "10.nnn.nnn.nnn"
                }, 
                {
                    "PrivateIPAddress": "10.nnn.nnn.nnn", 
                    "NodeRole": "COMPUTE-1", 
                    "PublicIPAddress": "10.nnn.nnn.nnn"
                }
            ], 
            "AutomatedSnapshotRetentionPeriod": 1, 
            "PreferredMaintenanceWindow": "wed:05:30-wed:06:00", 
            "AvailabilityZone": "us-east-1a", 
            "NodeType": "ds2.xlarge", 
            "ClusterPublicKey": "ssh-rsa AAAABexamplepublickey...Y3TAl Amazon-Redshift", 
             ...
             ...
}
```

To retrieve the cluster public key and cluster node IP addresses for your cluster using the Amazon Redshift API, use the `DescribeClusters` action\. For more information, see [describe\-clusters](https://docs.aws.amazon.com/cli/latest/reference/redshift/describe-clusters.html) in the *Amazon Redshift CLI Guide* or [DescribeClusters](https://docs.aws.amazon.com/redshift/latest/APIReference/API_DescribeClusters.html) in the Amazon Redshift API Guide\. 