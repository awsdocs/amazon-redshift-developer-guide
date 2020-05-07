# Step 1: Configure IAM permissions<a name="load-from-emr-steps-configure-iam"></a>

The users that create the Amazon EMR cluster and run the Amazon Redshift COPY command must have the necessary permissions\.

**To configure IAM permissions**

1. Add the following permissions for the IAM user that will create the Amazon EMR cluster\.

   ```
   ec2:DescribeSecurityGroups
   ec2:RevokeSecurityGroupIngress
   ec2:AuthorizeSecurityGroupIngress
   redshift:DescribeClusters
   ```

1. Add the following permission for the IAM role or IAM user that will execute the COPY command\.

   ```
   elasticmapreduce:ListInstances
   ```

1. Add the following permission to the Amazon EMR cluster's IAM role\.

   ```
   redshift:DescribeClusters
   ```