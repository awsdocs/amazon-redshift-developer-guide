# Step 5: Create a manifest file<a name="load-from-host-steps-create-manifest"></a>

The COPY command can connect to multiple hosts using SSH, and can create multiple SSH connections to each host\. COPY executes a command through each host connection, and then loads the output from the commands in parallel into the table\. The manifest file is a text file in JSON format that Amazon Redshift uses to connect to the host\. The manifest file specifies the SSH host endpoints and the commands that are executed on the hosts to return data to Amazon Redshift\. Optionally, you can include the host public key, the login user name, and a mandatory flag for each entry\.

Create the manifest file on your local computer\. In a later step, you upload the file to Amazon S3\. 

The manifest file is in the following format:

```
{ 
   "entries": [ 
     {"endpoint":"<ssh_endpoint_or_IP>", 
       "command": "<remote_command>",
       "mandatory":true, 
       "publickey": "<public_key>", 
       "username": "<host_user_name>"}, 
     {"endpoint":"<ssh_endpoint_or_IP>", 
       "command": "<remote_command>",
       "mandatory":true, 
       "publickey": "<public_key>", 
       "username": "host_user_name"} 
    ] 
}
```

The manifest file contains one "entries" construct for each SSH connection\. Each entry represents a single SSH connection\. You can have multiple connections to a single host or multiple connections to multiple hosts\. The double quotation marks are required as shown, both for the field names and the values\. The only value that does not need double quotation marks is the Boolean value **true** or **false** for the mandatory field\. 

The following describes the fields in the manifest file\. 

endpoint  
The URL address or IP address of the host\. For example, "`ec2-111-222-333.compute-1.amazonaws.com`" or "`22.33.44.56`" 

command   
The command that will be executed by the host to generate text or binary \(gzip, lzop, or bzip2\) output\. The command can be any command that the user *"host\_user\_name"* has permission to run\. The command can be as simple as printing a file, or it could query a database or launch a script\. The output \(text file, gzip binary file, lzop binary file, or bzip2 binary file\) must be in a form the Amazon Redshift COPY command can ingest\. For more information, see [Preparing your input data](t_preparing-input-data.md)\.

publickey  
\(Optional\) The public key of the host\. If provided, Amazon Redshift will use the public key to identify the host\. If the public key is not provided, Amazon Redshift will not attempt host identification\. For example, if the remote host's public key is: `ssh-rsa AbcCbaxxx…xxxDHKJ root@amazon.com` enter the following text in the publickey field: `AbcCbaxxx…xxxDHKJ`\. 

mandatory  
\(Optional\) Indicates whether the COPY command should fail if the connection fails\. The default is `false`\. If Amazon Redshift does not successfully make at least one connection, the COPY command fails\.

username  
\(Optional\) The username that will be used to log on to the host system and execute the remote command\. The user login name must be the same as the login that was used to add the public key to the host's authorized keys file in Step 2\. The default username is "redshift"\.

The following example shows a completed manifest to open four connections to the same host and execute a different command through each connection:

```
{ 
  "entries": [ 
       {"endpoint":"ec2-184-72-204-112.compute-1.amazonaws.com", 
          "command": "cat loaddata1.txt", 
          "mandatory":true, 
          "publickey": "ec2publickeyportionoftheec2keypair", 
          "username": "ec2-user"}, 
       {"endpoint":"ec2-184-72-204-112.compute-1.amazonaws.com", 
          "command": "cat loaddata2.txt", 
          "mandatory":true, 
          "publickey": "ec2publickeyportionoftheec2keypair", 
          "username": "ec2-user"},
       {"endpoint":"ec2-184-72-204-112.compute-1.amazonaws.com", 
          "command": "cat loaddata3.txt", 
          "mandatory":true, 
          "publickey": "ec2publickeyportionoftheec2keypair", 
          "username": "ec2-user"},
       {"endpoint":"ec2-184-72-204-112.compute-1.amazonaws.com", 
          "command": "cat loaddata4.txt", 
          "mandatory":true, 
          "publickey": "ec2publickeyportionoftheec2keypair", 
          "username": "ec2-user"}
     ] 
}
```