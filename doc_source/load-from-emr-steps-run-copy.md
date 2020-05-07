# Step 6: Run the COPY command to load the data<a name="load-from-emr-steps-run-copy"></a>

Run a [COPY](r_COPY.md) command to connect to the Amazon EMR cluster and load the data into an Amazon Redshift table\. The Amazon EMR cluster must continue running until the COPY command completes\. For example, do not configure the cluster to auto\-terminate\. 

**Important**  
If any of the data files are changed or deleted before the COPY completes, you might have unexpected results, or the COPY operation might fail\.

In the COPY command, specify the Amazon EMR cluster ID and the HDFS file path and file name\. 

```
copy sales
from 'emr://myemrclusterid/myoutput/part*' credentials 
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole';
```

You can use the wildcard characters asterisk \( `*` \) and question mark \( `?` \) as part of the file name argument\. For example, `part*` loads the files `part-0000`, `part-0001`, and so on\. If you specify only a folder name, COPY attempts to load all files in the folder\.

**Important**  
If you use wildcard characters or use only the folder name, verify that no unwanted files will be loaded or the COPY command will fail\. For example, some processes might write a log file to the output folder\.