# CREATE LIBRARY<a name="r_CREATE_LIBRARY"></a>

Installs a Python library, which is available for users to incorporate when creating a user\-defined function \(UDF\) with the [CREATE FUNCTION](r_CREATE_FUNCTION.md) command\. The total size of user\-installed libraries can't exceed 100 MB\. 

CREATE LIBRARY can't be run inside a transaction block \(BEGIN … END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

Amazon Redshift supports Python version 2\.7\. For more information, go to [www\.python\.org](https://www.python.org/)\.

For more information, see [Importing custom Python library modules](udf-python-language-support.md#udf-importing-custom-python-library-modules)\. 

## Syntax<a name="r_CREATE_LIBRARY-synopsis"></a>

```
CREATE [ OR REPLACE ] LIBRARY library_name LANGUAGE plpythonu
FROM
{ 'https://file_url'
| 's3://bucketname/file_name'
authorization
  [ REGION [AS] 'aws_region']
}
```

## Parameters<a name="r_CREATE_LIBRARY-parameters"></a>

OR REPLACE  
Specifies that if a library with the same name as this one already exists, the existing library is replaced\. REPLACE commits immediately\. If a UDF that depends on the library is running concurrently, the UDF might fail or return unexpected results, even if the UDF is running within a transaction\. You must be the owner or a superuser to replace a library\.

 *library\_name*   
The name of the library to be installed\. You can't create a library that contains a module with the same name as a Python Standard Library module or an Amazon Redshift preinstalled Python module\. If an existing user\-installed library uses the same Python package as the library to be installed , you must drop the existing library before installing the new library\. For more information, see [Python language support for UDFs](udf-python-language-support.md)\.

LANGUAGE plpythonu  
The language to use\. Python \(plpythonu\) is the only supported language\. Amazon Redshift supports Python version 2\.7\. For more information, go to [www\.python\.org](https://www.python.org/)\.

FROM  
The location of the library file\. You can specify an Amazon S3 bucket and object name, or you can specify a URL to download the file from a public website\. The library must be packaged in the form of a `.zip` file\. For more information, go to [Building and Installing Python Modules](https://docs.python.org/2/library/distutils.html?highlight=distutils#module-distutils) in the Python documentation\.

 https://*file\_url*   
The URL to download the file from a public website\. The URL can contain up to three redirects\. The following is an example of a file URL\.  

```
'https://www.example.com/pylib.zip'
```

 s3://*bucket\_name/file\_name*   
The path to a single Amazon S3 object that contains the library file\. The following is an example of an Amazon S3 object path\.  

```
's3://mybucket/my-pylib.zip'
```
If you specify an Amazon S3 bucket, you must also provide credentials for an AWS user that has permission to download the file\.   
 If the Amazon S3 bucket doesn't reside in the same AWS Region as your Amazon Redshift cluster, you must use the REGION option to specify the AWS Region in which the data is located\. The value for *aws\_region* must match an AWS Region listed in the table in the [REGION](copy-parameters-data-source-s3.md#copy-region) parameter description for the COPY command\.

*authorization*   
A clause that indicates the method your cluster uses for authentication and authorization to access the Amazon S3 bucket that contains the library file\. Your cluster must have permission to access the Amazon S3 with the LIST and GET actions\.  
The syntax for authorization is the same as for the COPY command authorization\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.  
To specify an AWS Identity and Access Management \(IAM\) role, replace *<account\-id>* and *<role\-name>* with the account ID and role name in the CREDENTIALS *credentials\-args* string\. An example is shown following\.  

```
'aws_iam_role=arn:aws:iam::<aws-account-id>:role/<role-name>'
```
Optionally, if the Amazon S3 bucket uses server\-side encryption, provide the encryption key in the credentials\-args string\. If you use temporary security credentials, provide the temporary token in the *credentials\-args* string\.  
To specify key\-based access control, provide the *credentials\-args* in the following format\.  

```
'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>'
```
To use temporary token credentials, you must provide the temporary access key ID, the temporary secret access key, and the temporary token\. The *credentials\-args* string is in the following format\.   

```
WITH CREDENTIALS AS 
'aws_access_key_id=<temporary-access-key-id>;aws_secret_access_key=<temporary-secret-access-key>;token=<temporary-token>'
```
For more information, see [Temporary security credentials](copy-usage_notes-access-permissions.md#r_copy-temporary-security-credentials)

 REGION \[AS\] *aws\_region*   
The AWS Region where the Amazon S3 bucket is located\. REGION is required when the Amazon S3 bucket isn't in the same AWS Region as the Amazon Redshift cluster\. The value for *aws\_region* must match an AWS Region listed in the table in the [REGION](copy-parameters-data-source-s3.md#copy-region) parameter description for the COPY command\.  
By default, CREATE LIBRARY assumes that the Amazon S3 bucket is located in the same AWS Region as the Amazon Redshift cluster\.

## Examples<a name="r_CREATE_LIBRARY-examples"></a>

The following two examples install the [urlparse](https://docs.python.org/2/library/urlparse.html#module-urlparse) Python module, which is packaged in a file named `urlparse3-1.0.3.zip`\. 

The following command installs a UDF library named `f_urlparse` from a package that has been uploaded to an Amazon S3 bucket located in the US East region\.

```
create library f_urlparse 
language plpythonu 
from 's3://mybucket/urlparse3-1.0.3.zip' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>'
region as 'us-east-1';
```

The following example installs a library named `f_urlparse` from a library file on a website\.

```
create library f_urlparse 
language plpythonu 
from 'https://example.com/packages/urlparse3-1.0.3.zip';
```