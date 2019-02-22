# COPY from Columnar Data Formats<a name="copy-usage_notes-copy-from-columnar"></a>

COPY can load data from Amazon S3 in the following columnar formats:
+ ORC 
+ Parquet 

COPY supports columnar formatted data with the following restrictions:
+ The cluster must be in one of the following AWS Regions: 
  + US East \(N\. Virginia\) Region \(us\-east\-1\)
  + US East \(Ohio\) Region \(us\-east\-2\)
  + US West \(N\. California\) Region \(us\-west\-1\)
  + US West \(Oregon\) Region \(us\-west\-2\) 
  + Asia Pacific \(Mumbai\) Region \(ap\-south\-1\)
  + Asia Pacific \(Seoul\) Region \(ap\-northeast\-2\)
  + Asia Pacific \(Singapore\) Region \(ap\-southeast\-1\)
  + Asia Pacific \(Sydney\) Region \(ap\-southeast\-2\)
  + Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)
  + Canada \(Central\) Region \(ca\-central\-1\)
  + EU \(Frankfurt\) Region \(eu\-central\-1\)
  + EU \(Ireland\) Region \(eu\-west\-1\)
  + EU \(London\) Region \(eu\-west\-2\)
  + South America \(São Paulo\) Region \(sa\-east\-1\)
+ The Amazon S3 bucket must be in the same region as the Amazon Redshift cluster\. 
+ COPY command credentials must be supplied using an AWS Identity and Access Management \(IAM\) role as an argument for the [IAM\_ROLE](copy-parameters-authorization.md#copy-iam-role) parameter or the [CREDENTIALS](copy-parameters-authorization.md#copy-credentials) parameter\. 
+ COPY doesn't automatically apply compression encodings\. 
+ Only the following COPY parameters are supported: 
  + [FROM](copy-parameters-data-source-s3.md#copy-parameters-from)
  + [IAM\_ROLE](copy-parameters-authorization.md#copy-iam-role)
  + [CREDENTIALS](copy-parameters-authorization.md#copy-credentials)
  + [STATUPDATE ](copy-parameters-data-load.md#copy-statupdate)
  + [MANIFEST](copy-parameters-data-source-s3.md#copy-manifest)
+ If COPY encounters an error while loading, the command fails\. ACCEPTANYDATE, ACCEPTINVCHARS, and MAXERROR aren't supported for columnar data types\.
+ Error messages are sent only to the SQL client\. Errors are not logged in STL\_LOAD\_ERRORS\.
+ COPY inserts values into the target table's columns in the same order as the columns occur in the columnar data files\. The number of columns in the target table and the number of columns in the data file must match\.
+ If the file you specify for the COPY operation includes one of the following extensions we will decompress the data without the need for adding any parameters: 
  + `.gz`
  + `.snappy`
  + `.bz2`