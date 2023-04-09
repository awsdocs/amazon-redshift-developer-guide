# COPY JOB \(preview\)<a name="r_COPY-JOB"></a>


|  | 
| --- |
| This is prerelease documentation for auto\-copy \(SQL COPY JOB\), which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only in test environments, and not in production environments\. Public preview will end on May 1, 2023\. Preview clusters and preview serverless workgroups and namespaces will be removed automatically two weeks after the end of the preview\. For preview terms and conditions, see Betas and Previews in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

For information about using this command in preview, see [Continuous file ingestion from Amazon S3 \(preview\)](loading-data-copy-job.md)\.

Manages COPY commands that load data into a table\. The COPY JOB command is an extension of the COPY command and automates data loading from Amazon S3 buckets\. When you create a COPY job, Amazon Redshift detects when new Amazon S3 files are created in a specified path, and then loads them automatically without your intervention\. The same parameters that are used in the original COPY command are used when loading the data\. Amazon Redshift keeps track of the loaded files to verify that they are loaded only one time\.

**Note**  
For information about the COPY command, including usage, parameters, and permissions, see [COPY](r_COPY.md)\.

## Required permission<a name="r_COPY-JOB-privileges"></a>

To run the COPY command of a COPY JOB, you must have INSERT privilege of the table being loaded\.

The IAM role specified with the COPY command must have permission to access the data to load\. For more information, see [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\.

## Syntax<a name="r_COPY-JOB-syntax"></a>

Create a copy job\. The parameters of the COPY command are saved with the copy job\.

```
COPY copy-command JOB CREATE job-name
[AUTO ON | OFF]
```

Change the configuration of a copy job\.

```
COPY JOB ALTER job-name
[AUTO ON | OFF]
```

Run a copy job\. The stored COPY command parameters are used\.

```
COPY JOB RUN job-name
```

List all copy jobs\.

```
COPY JOB LIST
```

Show the details of a copy job\.

```
COPY JOB SHOW job-name
```

Delete a copy job\.

```
COPY JOB DROP job-name
```

## Parameters<a name="r_COPY-JOB-parameters"></a>

*copy\-command*  
A COPY command that loads data from Amazon S3 to Amazon Redshift\. The clause contains COPY parameters that define the Amazon S3 bucket, target table, IAM role, and other parameters used when loading data\. All COPY command parameters for an Amazon S3 data load are supported except:  
+ The COPY JOB must be created using a COPY command that points to an empty Amazon S3 folder \(it doesn't contain any files\)\.
+ You cannot specify a COPY command with the MAXERROR or IGNOREALLERRORS options\.
+ You cannot specify a manifest file\. COPY JOB requires a designated Amazon S3 location to monitor for newly created files\.
+ You cannot specify an Amazon S3 file with the following columnar data formats: ORC and Parquet\.
+ You cannot specify a COPY command with authorization types like Access and Secret keys\. Only COPY commands that use the `IAM_ROLE` parameter for authorization are supported\. For more information, see [Authorization parameters](copy-parameters-authorization.md)\.
+ The COPY JOB doesn't support the default IAM role associated with the cluster\. You must specify the `IAM_ROLE` in the COPY command\. 
For more information, see [COPY from Amazon S3](copy-parameters-data-source-s3.md)\.

*job\-name*  
The name of the job used to reference the COPY job\.

 \[AUTO ON \| OFF\]   
Clause that indicates whether Amazon S3 data is automatically loaded into Amazon Redshift tables\.  
+ When `ON`, Amazon Redshift monitors the source Amazon S3 path for newly created files, and if found, a COPY command is run with the COPY parameters in the job definition\. This is the default\.
+ When `OFF`, Amazon Redshift does not run the COPY JOB automatically\.

## Usage notes<a name="r_COPY-JOB-usage-notes"></a>

The options of the COPY command aren't validated until run time\. For example, an invalid `IAM_ROLE` or an Amazon S3 data source results in runtime errors when the COPY JOB starts\.

If the cluster is paused, COPY JOBS are not run\.

To query COPY command files loaded and load errors, see [STL\_LOAD\_COMMITS](r_STL_LOAD_COMMITS.md), [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md), [STL\_LOADERROR\_DETAIL](r_STL_LOADERROR_DETAIL.md)\. For more information, see [Verifying that the data loaded correctly](verifying-that-data-loaded-correctly.md)\.

## Examples<a name="r_COPY-JOB-examples"></a>

The following example shows creating a COPY JOB to load data from an Amazon S3 bucket\.  

```
COPY public.target_table
FROM 's3://mybucket-bucket/staging-folder'
IAM_ROLE 'arn:aws:iam::123456789012:role/MyLoadRoleName' 
JOB CREATE my_copy_job_name
AUTO ON;
```