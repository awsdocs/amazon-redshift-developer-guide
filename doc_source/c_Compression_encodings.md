# Compression encodings<a name="c_Compression_encodings"></a>

<a name="compression-encoding-list"></a>A *compression encoding* specifies the type of compression that is applied to a column of data values as rows are added to a table\.

ENCODE AUTO is the default for tables\. Amazon Redshift automatically manages compression encoding for all columns in the table\. If you specify compression encoding for any column in the table, the table is no longer set to ENCODE AUTO\. Amazon Redshift no longer automatically manages compression encoding for all columns in the table\. You can specify the ENCODE AUTO option for the table to enable Amazon Redshift to automatically manage compression encoding for all columns in the table\. For more information, see [CREATE TABLE](r_CREATE_TABLE_NEW.md) and [ALTER TABLE](r_ALTER_TABLE.md)\.

If you specify compression encoding for any column in the table or if you don't specify the ENCODE AUTO option for the table, Amazon Redshift automatically assigns compression encoding to columns for which you don't specify compression encoding as follows:
+ Columns that are defined as sort keys are assigned RAW compression\.
+ Columns that are defined as BOOLEAN, REAL, or DOUBLE PRECISION data types are assigned RAW compression\.
+ Columns that are defined as SMALLINT, INTEGER, BIGINT, DECIMAL, DATE, TIMESTAMP, or TIMESTAMPTZ data types are assigned AZ64 compression\.
+ Columns that are defined as CHAR or VARCHAR data types are assigned LZO compression\.

The following table identifies the supported compression encodings and the data types that support the encoding\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Compression_encodings.html)