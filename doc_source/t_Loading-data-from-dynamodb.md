# Loading data from an Amazon DynamoDB table<a name="t_Loading-data-from-dynamodb"></a>

You can use the COPY command to load a table with data from a single Amazon DynamoDB table\.

**Important**  
The Amazon DynamoDB table that provides the data must be created in the same AWS Region as your cluster unless you use the [REGION](copy-parameters-data-source-s3.md#copy-region) option to specify the AWS Region in which the Amazon DynamoDB table is located\.

The COPY command leverages the Amazon Redshift massively parallel processing \(MPP\) architecture to read and load data in parallel from an Amazon DynamoDB table\. You can take maximum advantage of parallel processing by setting distribution styles on your Amazon Redshift tables\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\.

**Important**  
When the COPY command reads data from the Amazon DynamoDB table, the resulting data transfer is part of that table's provisioned throughput\.

To avoid consuming excessive amounts of provisioned read throughput, we recommend that you not load data from Amazon DynamoDB tables that are in production environments\. If you do load data from production tables, we recommend that you set the READRATIO option much lower than the average percentage of unused provisioned throughput\. A low READRATIO setting will help minimize throttling issues\. To use the entire provisioned throughput of an Amazon DynamoDB table, set READRATIO to 100\.

The COPY command matches attribute names in the items retrieved from the DynamoDB table to column names in an existing Amazon Redshift table by using the following rules:
+ Amazon Redshift table columns are case\-insensitively matched to Amazon DynamoDB item attributes\. If an item in the DynamoDB table contains multiple attributes that differ only in case, such as Price and PRICE, the COPY command will fail\.
+ Amazon Redshift table columns that do not match an attribute in the Amazon DynamoDB table are loaded as either NULL or empty, depending on the value specified with the EMPTYASNULL option in the [COPY](r_COPY.md) command\.
+ Amazon DynamoDB attributes that do not match a column in the Amazon Redshift table are discarded\. Attributes are read before they are matched, and so even discarded attributes consume part of that table's provisioned throughput\.
+ Only Amazon DynamoDB attributes with scalar STRING and NUMBER data types are supported\. The Amazon DynamoDB BINARY and SET data types are not supported\. If a COPY command tries to load an attribute with an unsupported data type, the command will fail\. If the attribute does not match an Amazon Redshift table column, COPY does not attempt to load it, and it does not raise an error\.

The COPY command uses the following syntax to load data from an Amazon DynamoDB table:

```
copy <redshift_tablename> from 'dynamodb://<dynamodb_table_name>'
authorization
readratio '<integer>';
```

The values for *authorization* are the AWS credentials needed to access the Amazon DynamoDB table\. If these credentials correspond to an IAM user, that IAM user must have permission to SCAN and DESCRIBE the Amazon DynamoDB table that is being loaded\.

The values for *authorization* provide the AWS authorization your cluster needs to access the Amazon DynamoDB table\. The permission must include SCAN and DESCRIBE for the Amazon DynamoDB table that is being loaded\. For more information about required permissions, see [IAM permissions for COPY, UNLOAD, and CREATE LIBRARY](copy-usage_notes-access-permissions.md#copy-usage_notes-iam-permissions)\. The preferred method for authentication is to specify the IAM\_ROLE parameter and provide the Amazon Resource Name \(ARN\) for an IAM role with the necessary permissions\. Alternatively, you can specify the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters and provide the access key ID and secret access key for an authorized IAM user as plain text\. For more information, see [Role\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-role-based) or [Key\-based access control](copy-usage_notes-access-permissions.md#copy-usage_notes-access-key-based)\. 

To authenticate using the IAM\_ROLE parameter, *<aws\-account\-id>* and *<role\-name>* as shown in the following syntax\. 

```
IAM_ROLE 'arn:aws:iam::<aws-account-id>:role/<role-name>'
```

The following example shows authentication using an IAM role\.

```
copy favoritemovies 
from 'dynamodb://ProductCatalog'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

To authenticate using IAM user credentials, replace *<access\-key\-id>* and *<secret\-access\-key* with an authorized user's access key ID and full secret access key for the ACCESS\_KEY\_ID and SECRET\_ACCESS\_KEY parameters as shown following\.

```
ACCESS_KEY_ID '<access-key-id>'
SECRET_ACCESS_KEY '<secret-access-key>';
```

The following example shows authentication using IAM user credentials\.

```
copy favoritemovies 
from 'dynamodb://ProductCatalog'
access_key_id '<access-key-id>'
secret_access_key '<secret-access-key';
```

For more information about other authorization options, see [Authorization parameters](copy-parameters-authorization.md)

If you want to validate your data without actually loading the table, use the NOLOAD option with the [COPY](r_COPY.md) command\.

The following example loads the FAVORITEMOVIES table with data from the DynamoDB table my\-favorite\-movies\-table\. The read activity can consume up to 50% of the provisioned throughput\.

```
copy favoritemovies from 'dynamodb://my-favorite-movies-table' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole' 
readratio 50;
```

To maximize throughput, the COPY command loads data from an Amazon DynamoDB table in parallel across the compute nodes in the cluster\.

## Provisioned throughput with automatic compression<a name="t_Loading-data-from-dynamodb-provisioned-throughput-with-automatic-compression"></a>

By default, the COPY command applies automatic compression whenever you specify an empty target table with no compression encoding\. The automatic compression analysis initially samples a large number of rows from the Amazon DynamoDB table\. The sample size is based on the value of the COMPROWS parameter\. The default is 100,000 rows per slice\.

After sampling, the sample rows are discarded and the entire table is loaded\. As a result, many rows are read twice\. For more information about how automatic compression works, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.

**Important**  
When the COPY command reads data from the Amazon DynamoDB table, including the rows used for sampling, the resulting data transfer is part of that table's provisioned throughput\.

## Loading multibyte data from Amazon DynamoDB<a name="t_Loading-data-from-dynamodb-loading-multibyte-data-from-amazon-dynamodb"></a>

If your data includes non\-ASCII multibyte characters \(such as Chinese or Cyrillic characters\), you must load the data to VARCHAR columns\. The VARCHAR data type supports four\-byte UTF\-8 characters, but the CHAR data type only accepts single\-byte ASCII characters\. You cannot load five\-byte or longer characters into Amazon Redshift tables\. For more information about CHAR and VARCHAR, see [Data types](c_Supported_data_types.md)\.