# Unloading Data<a name="c_unloading_data"></a>


+ [Unloading Data to Amazon S3](t_Unloading_tables.md)
+ [Unloading Encrypted Data Files](t_unloading_encrypted_files.md)
+ [Unloading Data in Delimited or Fixed\-Width Format](t_unloading_fixed_width_data.md)
+ [Reloading Unloaded Data](t_Reloading_unload_files.md)

To unload data from database tables to a set of files in an Amazon S3 bucket, you can use the [UNLOAD](r_UNLOAD.md) command with a SELECT statement\. You can unload text data in either delimited format or fixed\-width format, regardless of the data format that was used to load it\. You can also specify whether to create compressed GZIP files\.

You can limit the access users have to your Amazon S3 bucket by using temporary security credentials\.

**Important**  
The Amazon S3 bucket where Amazon Redshift will write the output files must be created in the same region as your cluster\.