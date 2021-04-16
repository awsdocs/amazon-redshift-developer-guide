# Monitoring metrics in Amazon Redshift Spectrum<a name="c-spectrum-metrics"></a>

You can monitor Amazon Redshift Spectrum queries using the following system views:
+ [SVL\_S3QUERY](r_SVL_S3QUERY.md)

  Use the SVL\_S3QUERY view to get details about Redshift Spectrum queries \(S3 queries\) at the segment and node slice level\.
+ [SVL\_S3QUERY\_SUMMARY](r_SVL_S3QUERY_SUMMARY.md)

  Use the SVL\_S3QUERY\_SUMMARY view to get a summary of all Amazon Redshift Spectrum queries \(S3 queries\) that have been run on the system\.

The following are some things to look for in SVL\_S3QUERY\_SUMMARY: 
+ The number of files that were processed by the Redshift Spectrum query\. 
+ The number of bytes scanned from Amazon S3\. The cost of a Redshift Spectrum query is reflected in the amount of data scanned from Amazon S3\.
+ The number of bytes returned from the Redshift Spectrum layer to the cluster\. A large amount of data returned might affect system performance\. 
+ The maximum duration and average duration of Redshift Spectrum requests\. Long\-running requests might indicate a bottleneck\. 