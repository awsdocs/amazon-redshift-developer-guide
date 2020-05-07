# Verify data files before and after a load<a name="c_best-practices-verifying-data-files"></a>

When you load data from Amazon S3, first upload your files to your Amazon S3 bucket, then verify that the bucket contains all the correct files, and only those files\. For more information, see [Verifying that the correct files are present in your bucket](verifying-that-correct-files-are-present.md)\. 

After the load operation is complete, query the [STL\_LOAD\_COMMITS](r_STL_LOAD_COMMITS.md) system table to verify that the expected files were loaded\. For more information, see [Verifying that the data loaded correctly](verifying-that-data-loaded-correctly.md)\. 