# Loading multibyte data from Amazon S3<a name="t_loading_unicode_data"></a>

If your data includes non\-ASCII multibyte characters \(such as Chinese or Cyrillic characters\), you must load the data to VARCHAR columns\. The VARCHAR data type supports four\-byte UTF\-8 characters, but the CHAR data type only accepts single\-byte ASCII characters\. You cannot load five\-byte or longer characters into Amazon Redshift tables\. For more information about CHAR and VARCHAR, see [Data types](c_Supported_data_types.md)\.

To check which encoding an input file uses, use the Linux * `file` * command: 

```
$ file ordersdata.txt
ordersdata.txt: ASCII English text
$ file uni_ordersdata.dat
uni_ordersdata.dat: UTF-8 Unicode text
```