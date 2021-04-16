# COPY from remote host \(SSH\)<a name="copy-parameters-data-source-ssh"></a>

You can use the COPY command to load data in parallel from one or more remote hosts, such Amazon Elastic Compute Cloud \(Amazon EC2\) instances or other computers\. COPY connects to the remote hosts using Secure Shell \(SSH\) and executes commands on the remote hosts to generate text output\. The remote host can be an EC2 Linux instance or another Unix or Linux computer configured to accept SSH connections\. Amazon Redshift can connect to multiple hosts, and can open multiple SSH connections to each host\. Amazon Redshift sends a unique command through each connection to generate text output to the host's standard output, which Amazon Redshift then reads as it does a text file\.

Use the FROM clause to specify the Amazon S3 object key for the manifest file that provides the information COPY uses to open SSH connections and run the remote commands\. 

**Topics**
+ [Syntax](#copy-parameters-data-source-ssh-syntax)
+ [Examples](#copy-parameters-data-source-ssh-examples)
+ [Parameters](#copy-parameters-data-source-ssh-parameters)
+ [Optional parameters](#copy-parameters-data-source-ssh-optional-parms)
+ [Unsupported parameters](#copy-parameters-data-source-ssh-unsupported-parms)

**Important**  
 If the S3 bucket that holds the manifest file doesn't reside in the same AWS Region as your cluster, you must use the REGION parameter to specify the Region in which the bucket is located\. 

## Syntax<a name="copy-parameters-data-source-ssh-syntax"></a>

```
FROM 's3://'ssh_manifest_file' }
authorization
SSH
| optional-parameters
```

## Examples<a name="copy-parameters-data-source-ssh-examples"></a>

The following example uses a manifest file to load data from a remote host using SSH\. 

```
copy sales
from 's3://mybucket/ssh_manifest' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
ssh;
```

## Parameters<a name="copy-parameters-data-source-ssh-parameters"></a>

FROM  
The source of the data to be loaded\. 

's3://*copy\_from\_ssh\_manifest\_file*'  <a name="copy-ssh-manifest"></a>
The COPY command can connect to multiple hosts using SSH, and can create multiple SSH connections to each host\. COPY executes a command through each host connection, and then loads the output from the commands in parallel into the table\. The *s3://copy\_from\_ssh\_manifest\_file* argument specifies the Amazon S3 object key for the manifest file that provides the information COPY uses to open SSH connections and run the remote commands\.  
The *s3://copy\_from\_ssh\_manifest\_file* argument must explicitly reference a single file; it can't be a key prefix\. The following shows an example:  

```
's3://mybucket/ssh_manifest.txt'
```
The manifest file is a text file in JSON format that Amazon Redshift uses to connect to the host\. The manifest file specifies the SSH host endpoints and the commands that will be executed on the hosts to return data to Amazon Redshift\. Optionally, you can include the host public key, the login user name, and a mandatory flag for each entry\. The following example shows a manifest file that creates two SSH connections:   

```
{ 
    "entries": [ 
	    {"endpoint":"<ssh_endpoint_or_IP>", 
           "command": "<remote_command>",
           "mandatory":true, 
           "publickey": “<public_key>”, 
           "username": “<host_user_name>”}, 
	    {"endpoint":"<ssh_endpoint_or_IP>", 
           "command": "<remote_command>",
           "mandatory":true, 
           "publickey": “<public_key>”, 
           "username": “<host_user_name>”} 
     ] 
}
```
The manifest file contains one `"entries"` construct for each SSH connection\. You can have multiple connections to a single host or multiple connections to multiple hosts\. The double quotation mark characters are required as shown, both for the field names and the values\. The quotation mark characters must be simple quotation marks \(0x22\), not slanted or "smart" quotation marks\. The only value that doesn't need double quotation mark characters is the Boolean value `true` or `false` for the `"mandatory"` field\.   
The following list describes the fields in the manifest file\.     
endpoint  <a name="copy-ssh-manifest-endpoint"></a>
The URL address or IP address of the host—for example, `"ec2-111-222-333.compute-1.amazonaws.com"`, or `"198.51.100.0"`\.   
command  <a name="copy-ssh-manifest-command"></a>
The command to be executed by the host to generate text output or binary output in gzip, lzop, bzip2, or zstd format\. The command can be any command that the user *"host\_user\_name"* has permission to run\. The command can be as simple as printing a file, or it can query a database or launch a script\. The output \(text file, gzip binary file, lzop binary file, or bzip2 binary file\) must be in a form that the Amazon Redshift COPY command can ingest\. For more information, see [Preparing your input data](t_preparing-input-data.md)\.  
publickey  <a name="copy-ssh-manifest-publickey"></a>
\(Optional\) The public key of the host\. If provided, Amazon Redshift will use the public key to identify the host\. If the public key isn't provided, Amazon Redshift will not attempt host identification\. For example, if the remote host's public key is `ssh-rsa AbcCbaxxx…Example root@amazon.com`, type the following text in the public key field: `"AbcCbaxxx…Example"`  
mandatory  <a name="copy-ssh-manifest-mandatory"></a>
\(Optional\) A clause that indicates whether the COPY command should fail if the connection attempt fails\. The default is `false`\. If Amazon Redshift doesn't successfully make at least one connection, the COPY command fails\.  
username  <a name="copy-ssh-manifest-username"></a>
\(Optional\) The user name that will be used to log on to the host system and run the remote command\. The user login name must be the same as the login that was used to add the Amazon Redshift cluster's public key to the host's authorized keys file\. The default username is `redshift`\.
For more information about creating a manifest file, see [Loading data process](loading-data-from-remote-hosts.md#load-from-host-process)\.  
To COPY from a remote host, the SSH parameter must be specified with the COPY command\. If the SSH parameter isn't specified, COPY assumes that the file specified with FROM is a data file and will fail\.   
If you use automatic compression, the COPY command performs two data read operations, which means it will run the remote command twice\. The first read operation is to provide a data sample for compression analysis, then the second read operation actually loads the data\. If executing the remote command twice might cause a problem, you should disable automatic compression\. To disable automatic compression, run the COPY command with the COMPUPDATE parameter set to OFF\. For more information, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.  
For detailed procedures for using COPY from SSH, see [Loading data from remote hosts](loading-data-from-remote-hosts.md)\.

*authorization*  
The COPY command needs authorization to access data in another AWS resource, including in Amazon S3, Amazon EMR, Amazon DynamoDB, and Amazon EC2\. You can provide that authorization by referencing an AWS Identity and Access Management \(IAM\) role that is attached to your cluster \(role\-based access control\) or by providing the access credentials for an IAM user \(key\-based access control\)\. For increased security and flexibility, we recommend using IAM role\-based access control\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.

SSH  <a name="copy-ssh"></a>
A clause that specifies that data is to be loaded from a remote host using the SSH protocol\. If you specify SSH, you must also provide a manifest file using the [s3://copy_from_ssh_manifest_file](#copy-ssh-manifest) argument\.   
If you are using SSH to copy from a host using a private IP address in a remote VPC, the VPC must have enhanced VPC routing enabled\. For more information about Enhanced VPC routing, see [Amazon Redshift Enhanced VPC Routing](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-routing.html)\.

## Optional parameters<a name="copy-parameters-data-source-ssh-optional-parms"></a>

You can optionally specify the following parameters with COPY from SSH: 
+ [Column mapping options](copy-parameters-column-mapping.md)
+ [Data format parameters](copy-parameters-data-format.md#copy-data-format-parameters)
+ [Data conversion parameters](copy-parameters-data-conversion.md)
+ [ Data load operations](copy-parameters-data-load.md)

## Unsupported parameters<a name="copy-parameters-data-source-ssh-unsupported-parms"></a>

You can't use the following parameters with COPY from SSH: 
+ ENCRYPTED
+ MANIFEST
+ READRATIO