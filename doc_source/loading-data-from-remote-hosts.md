# Loading data from remote hosts<a name="loading-data-from-remote-hosts"></a>

You can use the COPY command to load data in parallel from one or more remote hosts, such as Amazon EC2 instances or other computers\. COPY connects to the remote hosts using SSH and executes commands on the remote hosts to generate text output\. 

The remote host can be an Amazon EC2 Linux instance or another Unix or Linux computer configured to accept SSH connections\. This guide assumes your remote host is an Amazon EC2 instance\. Where the procedure is different for another computer, the guide will point out the difference\. 

Amazon Redshift can connect to multiple hosts, and can open multiple SSH connections to each host\. Amazon Redshifts sends a unique command through each connection to generate text output to the host's standard output, which Amazon Redshift then reads as it would a text file\.

## Before you begin<a name="load-from-host-before-you-begin"></a>

Before you begin, you should have the following in place: 
+ One or more host machines, such as Amazon EC2 instances, that you can connect to using SSH\.
+ Data sources on the hosts\. 

  You will provide commands that the Amazon Redshift cluster will run on the hosts to generate the text output\. After the cluster connects to a host, the COPY command runs the commands, reads the text from the hosts' standard output, and loads the data in parallel into an Amazon Redshift table\. The text output must be in a form that the COPY command can ingest\. For more information, see [Preparing your input data](t_preparing-input-data.md)
+ Access to the hosts from your computer\. 

  For an Amazon EC2 instance, you will use an SSH connection to access the host\. You will need to access the host to add the Amazon Redshift cluster's public key to the host's authorized keys file\.
+ A running Amazon Redshift cluster\. 

  For information about how to launch a cluster, see [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/)\. 

## Loading data process<a name="load-from-host-process"></a>

This section walks you through the process of loading data from remote hosts\. The following sections provide the details you need to accomplish each step\.
+ **[Step 1: Retrieve the cluster public key and cluster node IP addresses](load-from-host-steps-retrieve-key-and-ips.md)**

  The public key enables the Amazon Redshift cluster nodes to establish SSH connections to the remote hosts\. You will use the IP address for each cluster node to configure the host security groups or firewall to permit access from your Amazon Redshift cluster using these IP addresses\. 
+ **[Step 2: Add the Amazon Redshift cluster public key to the host's authorized keys file](load-from-host-steps-add-key-to-host.md)**

  You add the Amazon Redshift cluster public key to the host's authorized keys file so that the host will recognize the Amazon Redshift cluster and accept the SSH connection\. 
+ **[Step 3: Configure the host to accept all of the Amazon Redshift cluster's IP addresses](load-from-host-steps-configure-security-groups.md)** 

  For Amazon EC2 , modify the instance's security groups to add ingress rules to accept the Amazon Redshift IP addresses\. For other hosts, modify the firewall so that your Amazon Redshift nodes are able to establish SSH connections to the remote host\. 
+ **[Step 4: Get the public key for the host](load-from-host-steps-get-the-host-key.md)**

  You can optionally specify that Amazon Redshift should use the public key to identify the host\. You will need to locate the public key and copy the text into your manifest file\. 
+ **[Step 5: Create a manifest file](load-from-host-steps-create-manifest.md)** 

  The manifest is a JSON\-formatted text file with the details Amazon Redshift needs to connect to the hosts and fetch the data\. 
+ **[Step 6: Upload the manifest file to an Amazon S3 bucket](load-from-host-steps-upload-manifest.md)** 

  Amazon Redshift reads the manifest and uses that information to connect to the remote host\. If the Amazon S3 bucket does not reside in the same Region as your Amazon Redshift cluster, you must use the [REGION](copy-parameters-data-source-s3.md#copy-region) option to specify the Region in which the data is located\.
+ **[Step 7: Run the COPY command to load the data](load-from-host-steps-run-copy.md)**

  From an Amazon Redshift database, run the COPY command to load the data into an Amazon Redshift table\. 
