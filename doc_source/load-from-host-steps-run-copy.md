# Step 7: Run the COPY command to load the data<a name="load-from-host-steps-run-copy"></a>

Run a [COPY](r_COPY.md) command to connect to the host and load the data into an Amazon Redshift table\. In the COPY command, specify the explicit Amazon S3 object path for the manifest file and include the SSH option\. For example, 

```
copy sales
from 's3://mybucket/ssh_manifest' credentials 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|'
ssh;
```

**Note**  
If you use automatic compression, the COPY command performs two data reads, which means it executes the remote command twice\. The first read is to provide a sample for compression analysis, then the second read actually loads the data\. If executing the remote command twice might cause a problem because of potential side effects, you should disable automatic compression\. To disable automatic compression, run the COPY command with the COMPUPDATE option set to OFF\. For more information, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\. 