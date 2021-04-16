# Validating input data<a name="t_Validating_input_files"></a>

To validate the data in the Amazon S3 input files or Amazon DynamoDB table before you actually load the data, use the NOLOAD option with the [COPY](r_COPY.md) command\. Use NOLOAD with the same COPY commands and options you would use to actually load the data\. NOLOAD checks the integrity of all of the data without loading it into the database\. The NOLOAD option displays any errors that would occur if you had attempted to load the data\.

For example, if you specified the incorrect Amazon S3 path for the input file, Amazon Redshift would display the following error:

```
ERROR:  No such file or directory
DETAIL:
-----------------------------------------------
Amazon Redshift error:  The specified key does not exist
code:      2
context:   S3 key being read :
location:  step_scan.cpp:1883
process:   xenmaster [pid=22199]
-----------------------------------------------
```

To troubleshoot error messages, see the [Load error reference](r_Load_Error_Reference.md)\. 