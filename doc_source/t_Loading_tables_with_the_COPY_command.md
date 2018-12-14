# Using a COPY Command to Load Data<a name="t_Loading_tables_with_the_COPY_command"></a>

**Topics**
+ [Credentials and Access Permissions](loading-data-access-permissions.md)
+ [Preparing Your Input Data](t_preparing-input-data.md)
+ [Loading Data from Amazon S3](t_Loading-data-from-S3.md)
+ [Loading Data from Amazon EMR](loading-data-from-emr.md)
+ [Loading Data from Remote Hosts](loading-data-from-remote-hosts.md)
+ [Loading Data from an Amazon DynamoDB Table](t_Loading-data-from-dynamodb.md)
+ [Verifying That the Data Was Loaded Correctly](verifying-that-data-loaded-correctly.md)
+ [Validating Input Data](t_Validating_input_files.md)
+ [Loading Tables with Automatic Compression](c_Loading_tables_auto_compress.md)
+ [Optimizing Storage for Narrow Tables](c_load_compression_hidden_cols.md)
+ [Loading Default Column Values](c_loading_default_values.md)
+ [Troubleshooting Data Loads](t_Troubleshooting_load_errors.md)

The COPY command leverages the Amazon Redshift massively parallel processing \(MPP\) architecture to read and load data in parallel from files on Amazon S3, from a DynamoDB table, or from text output from one or more remote hosts\.

**Note**  
We strongly recommend using the COPY command to load large amounts of data\. Using individual INSERT statements to populate a table might be prohibitively slow\. Alternatively, if your data already exists in other Amazon Redshift database tables, use INSERT INTO \.\.\. SELECT or CREATE TABLE AS to improve performance\. For information, see [INSERT](r_INSERT_30.md) or [CREATE TABLE AS](r_CREATE_TABLE_AS.md)\.

To load data from another AWS resource, your cluster must have permission to access the resource and perform the necessary actions\. 

To grant or revoke privilege to load data into a table using a COPY command, grant or revoke the INSERT privilege\.

Your data needs to be in the proper format for loading into your Amazon Redshift table\. This section presents guidelines for preparing and verifying your data before the load and for validating a COPY statement before you execute it\.

To protect the information in your files, you can encrypt the data files before you upload them to your Amazon S3 bucket; COPY will decrypt the data as it performs the load\. You can also limit access to your load data by providing temporary security credentials to users\. Temporary security credentials provide enhanced security because they have short life spans and cannot be reused after they expire\.

You can compress the files using gzip, lzop, or bzip2 to save time uploading the files\. COPY can then speed up the load process by uncompressing the files as they are read\. 

To help keep your data secure in transit within the AWS cloud, Amazon Redshift uses hardware accelerated SSL to communicate with Amazon S3 or Amazon DynamoDB for COPY, UNLOAD, backup, and restore operations\.

When you load your table directly from an Amazon DynamoDB table, you have the option to control the amount of Amazon DynamoDB provisioned throughput you consume\.

You can optionally let COPY analyze your input data and automatically apply optimal compression encodings to your table as part of the load process\.