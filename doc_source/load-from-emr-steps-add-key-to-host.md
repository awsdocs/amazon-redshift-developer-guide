# Step 4: Add the Amazon Redshift cluster public key to each Amazon EC2 host's authorized keys file<a name="load-from-emr-steps-add-key-to-host"></a>

You add the cluster public key to each host's authorized keys file for all of the Amazon EMR cluster nodes so that the hosts will recognize Amazon Redshift and accept the SSH connection\. 

**To add the Amazon Redshift cluster public key to the host's authorized keys file**

1. Access the host using an SSH connection\. 

   For information about connecting to an instance using SSH, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide*\. 

1. Copy the Amazon Redshift public key from the console or from the CLI response text\. 

1. Copy and paste the contents of the public key into the `/home/<ssh_username>/.ssh/authorized_keys` file on the host\. Include the complete string, including the prefix "`ssh-rsa` " and suffix "`Amazon-Redshift`"\. For example: 

   ```
   ssh-rsa AAAACTP3isxgGzVWoIWpbVvRCOzYdVifMrh… uA70BnMHCaMiRdmvsDOedZDOedZ Amazon-Redshift
   ```