# Continuous file ingestion from Amazon S3 \(preview\)<a name="loading-data-copy-job"></a>


|  | 
| --- |
| This is prerelease documentation for auto\-copy \(SQL COPY JOB\), which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only in test environments, and not in production environments\. Public preview will end on February 28, 2023\. Preview clusters and preview serverless workgroups and namespaces will be removed automatically two weeks after the end of the preview\. For preview terms and conditions, see Betas and Previews in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

**Note**  
You can create an Amazon Redshift cluster in **Preview** to test new features of Amazon Redshift\. You can't use those features in production or move your **Preview** cluster to a production cluster or a cluster on another track\. For preview terms and conditions, see *Beta and Previews* in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.  
Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
On the navigation menu, choose **Provisioned clusters dashboard**, and choose **Clusters**\. The clusters for your account in the current AWS Region are listed\. A subset of properties of each cluster is displayed in columns in the list\.
A banner displays on the **Clusters** list page that introduces preview\. Choose the button **Create preview cluster** to open the create cluster page\.
Enter properties for your cluster\. Choose the **Preview track** that contains the features you want to test\. We recommend entering a name for the cluster that indicates that it is on a preview track\. Choose options for your cluster, including options labeled as **\-preview**, for the features you want to test\. For general information about creating clusters, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the *Amazon Redshift Management Guide*\.
Choose **Create preview cluster** to create a cluster in preview\.
When your preview cluster is available, use your SQL client to load and query data\.
Your cluster must be created with the preview track named: `preview_autocopy_2022`\. Use a new cluster for testing, restoring a cluster into this track is not supported\. The autocopy feature is not available with Amazon Redshift Serverless workgroup\.  
This preview is available in the following AWS Regions:  
US East \(Ohio\) Region \(us\-east\-2\)
US East \(N\. Virginia\) Region \(us\-east\-1\)
US West \(Oregon\) Region \(us\-west\-2\) 
Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)
Europe \(Stockholm\) Region \(eu\-north\-1\)
Europe \(Ireland\) Region \(eu\-west\-1\)

You can use a COPY JOB to load data into your Amazon Redshift tables from files that are stored in Amazon S3\. Amazon Redshift detects when new Amazon S3 files are added to the path specified in your COPY command\. A COPY command is then automatically run without you having to create an external data ingestion pipeline\. Amazon Redshift keeps track of which files have been loaded\. Amazon Redshift determines the number of files batched together per COPY command\. You can see the resulting COPY commands in system views\.

You define a COPY JOB one time\. The same parameters are used for future runs\.

You manage the load operations using options to CREATE, LIST, SHOW, DROP, ALTER, and RUN jobs\. For more information, see [COPY JOB \(preview\)](r_COPY-JOB.md)\.

You can query system views to see the COPY JOB status and progress\. Views are provided as follows:
+ [SYS\_COPY\_JOB \(preview\)](SYS_COPY_JOB.md) – contains a row for each currently defined COPY JOB\.
+ [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md) – contains errors from COPY commands\.
+ [STL\_LOAD\_COMMITS](r_STL_LOAD_COMMITS.md) – contains information used to troubleshoot a COPY command data load\.
+ [SYS\_LOAD\_HISTORY](SYS_LOAD_HISTORY.md) – contains details of COPY commands\.
+ [SYS\_LOAD\_ERROR\_DETAIL](SYS_LOAD_ERROR_DETAIL.md) – contains details of COPY command errors\.

To get the list of files loaded by a COPY JOB, run the following example replacing *<job\_id>*:

```
SELECT job_id, job_name, data_source, copy_query,filename,status, curtime
FROM sys_copy_job copyjob
JOIN stl_load_commits loadcommit
ON copyjob.job_id = loadcommit.copy_job_id
WHERE job_id = <job_id>;
```