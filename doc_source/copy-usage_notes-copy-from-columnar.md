# COPY from columnar data formats<a name="copy-usage_notes-copy-from-columnar"></a>

COPY can load data from Amazon S3 in the following columnar formats:
+ ORC 
+ Parquet 

COPY supports columnar formatted data with the following restrictions:
+ The cluster must be in one of the following AWS Regions: 
  + US East \(N\. Virginia\) Region \(us\-east\-1\)
  + US East \(Ohio\) Region \(us\-east\-2\)
  + US West \(N\. California\) Region \(us\-west\-1\)
  + US West \(Oregon\) Region \(us\-west\-2\) 
  + Asia Pacific \(Hong Kong\) Region \(ap\-east\-1\) 
  + Asia Pacific \(Mumbai\) Region \(ap\-south\-1\)
  + Asia Pacific \(Seoul\) Region \(ap\-northeast\-2\)
  + Asia Pacific \(Singapore\) Region \(ap\-southeast\-1\)
  + Asia Pacific \(Sydney\) Region \(ap\-southeast\-2\)
  + Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)
  + Canada \(Central\) Region \(ca\-central\-1\)
  + China \(Beijing\) Region \(cn\-north\-1\)
  + China \(Ningxia\) Region \(cn\-northwest\-1\)
  + Europe \(Frankfurt\) Region \(eu\-central\-1\)
  + Europe \(Ireland\) Region \(eu\-west\-1\)
  + Europe \(London\) Region \(eu\-west\-2\)
  + Europe \(Paris\) Region \(eu\-west\-3\)
  + Europe \(Stockholm\) Region \(eu\-north\-1\)
  + Middle East \(Bahrain\) Region \(me\-south\-1\)
  + South America \(São Paulo\) Region \(sa\-east\-1\)
  + AWS GovCloud \(US\-West\) \(us\-gov\-west\-1\)
+ The Amazon S3 bucket must be in the same AWS Region as the Amazon Redshift cluster\. 
+ To access your Amazon S3 data through a VPC endpoint, set up access using IAM policies and IAM roles as described in [Using Amazon Redshift Spectrum with Enhanced VPC Routing](https://docs.aws.amazon.com/redshift/latest/mgmt/spectrum-enhanced-vpc.html) in the *Amazon Redshift Cluster Management Guide*\. 
+ COPY doesn't automatically apply compression encodings\. 
+ Only the following COPY parameters are supported: 
  + [FROM](copy-parameters-data-source-s3.md#copy-parameters-from)
  + [IAM\_ROLE](copy-parameters-authorization.md#copy-iam-role)
  + [CREDENTIALS](copy-parameters-authorization.md#copy-credentials)
  + [STATUPDATE ](copy-parameters-data-load.md#copy-statupdate)
  + [MANIFEST](copy-parameters-data-source-s3.md#copy-manifest)
  + [ACCESS\_KEY\_ID, SECRET\_ACCESS\_KEY, and SESSION\_TOKEN](copy-parameters-authorization.md#copy-access-key-id)
+ If COPY encounters an error while loading, the command fails\. ACCEPTANYDATE, ACCEPTINVCHARS, and MAXERROR aren't supported for columnar data types\.
+ Error messages are sent only to the SQL client\. Errors aren't logged in STL\_LOAD\_ERRORS\.
+ COPY inserts values into the target table's columns in the same order as the columns occur in the columnar data files\. The number of columns in the target table and the number of columns in the data file must match\.
+ If the file you specify for the COPY operation includes one of the following extensions, we decompress the data without the need for adding any parameters: 
  + `.gz`
  + `.snappy`
  + `.bz2`

COPY from the Parquet and ORC file formats uses Redshift Spectrum and the bucket access\. For more information, see [Using Amazon Redshift Spectrum with enhanced VPC routing](https://docs.aws.amazon.com/redshift/latest/mgmt/spectrum-enhanced-vpc.html)\.