# Unloading data to Amazon S3<a name="t_Unloading_tables"></a>

Amazon Redshift splits the results of a select statement across a set of files, one or more files per node slice, to simplify parallel reloading of the data\. Alternatively, you can specify that [UNLOAD](r_UNLOAD.md) should write the results serially to one or more files by adding the PARALLEL OFF option\. You can limit the size of the files in Amazon S3 by specifying the MAXFILESIZE parameter\. UNLOAD automatically encrypts data files using Amazon S3 server\-side encryption \(SSE\-S3\)\. 

You can use any select statement in the UNLOAD command that Amazon Redshift supports, except for a select that uses a LIMIT clause in the outer select\. For example, you can use a select statement that includes specific columns or that uses a where clause to join multiple tables\. If your query contains quotation marks \(enclosing literal values, for example\), you need to escape them in the query text \(\\'\)\. For more information, see the [SELECT](r_SELECT_synopsis.md) command reference\. For more information about using a LIMIT clause, see the [Usage notes](r_UNLOAD.md#unload-usage-notes) for the UNLOAD command\.

For example, the following UNLOAD command sends the contents of the VENUE table to the Amazon S3 bucket `s3://mybucket/tickit/unload/`\.

```
unload ('select * from venue')   
to 's3://mybucket/tickit/unload/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

The file names created by the previous example include the prefix '`venue_`'\.

```
venue_0000_part_00
venue_0001_part_00
venue_0002_part_00
venue_0003_part_00
```

By default, UNLOAD writes data in parallel to multiple files, according to the number of slices in the cluster\. To write data to a single file, specify PARALLEL OFF\. UNLOAD writes the data serially, sorted absolutely according to the ORDER BY clause, if one is used\. The maximum size for a data file is 6\.2 GB\. If the data size is greater than the maximum, UNLOAD creates additional files, up to 6\.2 GB each\. 

The following example writes the contents VENUE to a single file\. Only one file is required because the file size is less than 6\.2 GB\.

```
unload ('select * from venue')
to 's3://mybucket/tickit/unload/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
parallel off;
```

**Note**  
The UNLOAD command is designed to use parallel processing\. We recommend leaving PARALLEL enabled for most cases, especially if the files will be used to load tables using a COPY command\.

Assuming the total data size for VENUE is 5 GB, the following example writes the contents of VENUE to 50 files, each 100 MB in size\.

```
unload ('select * from venue')
to 's3://mybucket/tickit/unload/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
parallel off
maxfilesize 100 mb;
```

If you include a prefix in the Amazon S3 path string, UNLOAD will use that prefix for the file names\. 

```
unload ('select * from venue')
to 's3://mybucket/tickit/unload/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

You can limit the access users have to your data by using temporary security credentials\. Temporary security credentials provide enhanced security because they have short life spans and cannot be reused after they expire\. A user who has these temporary security credentials can access your resources only until the credentials expire\. For more information, see [Temporary security credentials](copy-usage_notes-access-permissions.md#r_copy-temporary-security-credentials)\. To unload data using temporary access credentials, use the following syntax:

```
unload ('select * from venue')   
to 's3://mybucket/tickit/venue_' 
access_key_id '<access-key-id>'
secret_access_key '<secret-access-key>'
session_token '<temporary-token>';
```

**Important**  
The temporary security credentials must be valid for the entire duration of the UNLOAD statement\. If the temporary security credentials expire during the load process, the UNLOAD will fail and the transaction will be rolled back\. For example, if temporary security credentials expire after 15 minutes and the UNLOAD requires one hour, the UNLOAD will fail before it completes\.

You can create a manifest file that lists the unload files by specifying the MANIFEST option in the UNLOAD command\. The manifest is a text file in JSON format that explicitly lists the URL of each file that was written to Amazon S3\. 

The following example includes the manifest option\. 

```
unload ('select * from venue')
to 's3://mybucket/tickit/venue_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest;
```

The following example shows a manifest for four unload files\.

```
{
  "entries": [
    {"url":"s3://mybucket/tickit/venue_0000_part_00"},
    {"url":"s3://mybucket/tickit/venue_0001_part_00"},
    {"url":"s3://mybucket/tickit/venue_0002_part_00"},
    {"url":"s3://mybucket/tickit/venue_0003_part_00"}
  ]
}
```

The manifest file can be used to load the same files by using a COPY with the MANIFEST option\. For more information, see [Using a manifest to specify data files](loading-data-files-using-manifest.md)\.

After you complete an UNLOAD operation, confirm that the data was unloaded correctly by navigating to the Amazon S3 bucket where UNLOAD wrote the files\. You will see one or more numbered files per slice, starting with the number zero\. If you specified the MANIFEST option, you will also see a file ending with '`manifest`'\. For example:

```
mybucket/tickit/venue_0000_part_00 
mybucket/tickit/venue_0001_part_00 
mybucket/tickit/venue_0002_part_00 
mybucket/tickit/venue_0003_part_00
mybucket/tickit/venue_manifest
```

You can programmatically get a list of the files that were written to Amazon S3 by calling an Amazon S3 list operation after the UNLOAD completes; however, depending on how quickly you issue the call, the list might be incomplete because an Amazon S3 list operation is eventually consistent\. To get a complete, authoritative list immediately, query STL\_UNLOAD\_LOG\.

The following query returns the pathname for files that were created by an UNLOAD\. The [PG\_LAST\_QUERY\_ID](PG_LAST_QUERY_ID.md) function returns the most recent query\. 

```
select query, substring(path,0,40) as path
from stl_unload_log
where query=2320
order by path;

query |             path
-------+--------------------------------------
  2320 | s3://my-bucket/venue0000_part_00
  2320 | s3://my-bucket/venue0001_part_00
  2320 | s3://my-bucket/venue0002_part_00
  2320 | s3://my-bucket/venue0003_part_00
(4 rows)
```

If the amount of data is very large, Amazon Redshift might split the files into multiple parts per slice\. For example:

```
venue_0000_part_00
venue_0000_part_01
venue_0000_part_02
venue_0001_part_00
venue_0001_part_01
venue_0001_part_02
...
```

The following UNLOAD command includes a quoted string in the select statement, so the quotation marks are escaped \(`=\'OH\' '`\)\.

```
unload ('select venuename, venuecity from venue where venuestate=\'OH\' ')
to 's3://mybucket/tickit/venue/ ' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

By default, UNLOAD will fail rather than overwrite existing files in the destination bucket\. To overwrite the existing files, including the manifest file, specify the ALLOWOVERWRITE option\.

```
unload ('select * from venue') 
to 's3://mybucket/venue_pipe_' 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
manifest 
allowoverwrite;
```