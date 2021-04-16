# Workload management<a name="c_workload_mngmt_classification"></a>

Amazon Redshift workload management \(WLM\) enables users to flexibly manage priorities within workloads so that short, fast\-running queries won't get stuck in queues behind long\-running queries\.

Amazon Redshift WLM creates query queues at runtime according to *service classes*, which define the configuration parameters for various types of queues, including internal system queues and user\-accessible queues\. From a user perspective, a user\-accessible service class and a queue are functionally equivalent\. For consistency, this documentation uses the term *queue* to mean a user\-accessible service class as well as a runtime queue\.

When you run a query, WLM assigns the query to a queue according to the user's user group or by matching a query group that is listed in the queue configuration with a query group label that the user sets at runtime\.


|  | 
| --- |
|  Currently, the default for clusters using the default parameter group is to use automatic WLM\. Automatic WLM manages query concurrency and memory allocation\. For more information, see [Implementing automatic WLM](automatic-wlm.md)\.   | 

With manual WLM, Amazon Redshift configures one queue with a *concurrency level* of five, which enables up to five queries to run concurrently, plus one predefined Superuser queue, with a concurrency level of one\. You can define up to eight queues\. Each queue can be configured with a maximum concurrency level of 50\. The maximum total concurrency level for all user\-defined queues \(not including the Superuser queue\) is 50\.

The easiest way to modify the WLM configuration is by using the Amazon Redshift Management Console\. You can also use the Amazon Redshift command line interface \(CLI\) or the Amazon Redshift API\.

For more information about implementing and using workload management, see [Implementing workload management](cm-c-implementing-workload-management.md)\.