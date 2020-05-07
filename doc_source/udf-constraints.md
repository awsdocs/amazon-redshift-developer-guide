# UDF constraints<a name="udf-constraints"></a>

Within the constraints listed in this topic, you can use UDFs anywhere you use the Amazon Redshift built\-in scalar functions\. For more information, see [SQL functions reference](c_SQL_functions.md)\.

Amazon Redshift Python UDFs have the following constraints:
+ Python UDFs cannot access the network or read or write to the file system\.
+ The total size of user\-installed Python libraries cannot exceed 100 MB\.
+ The number of Python UDFs that can run concurrently per cluster is limited to one\-fourth of the total concurrency level for the cluster\. For example, if the cluster is configured with a concurrency of 15, a maximum of three UDFs can run concurrently\. After the limit is reached, UDFs are queued for execution within workload management queues\. SQL UDFs don't have a concurrency limit\. For more information, see [Implementing workload management](cm-c-implementing-workload-management.md)\.