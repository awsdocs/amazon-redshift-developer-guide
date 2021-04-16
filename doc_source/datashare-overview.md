# Sharing data across clusters \(preview\)<a name="datashare-overview"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

With *data sharing*, you can securely and easily share live data across Amazon Redshift clusters for read purposes\. Data sharing improves the agility of your organization by giving you instant, granular, and high\-performance access to data across Amazon Redshift clusters without your needing to manually copy or move it\. With data sharing, you have live access to data so that your users can see the most up\-to\-date and consistent information as it's updated in Amazon Redshift clusters\. 

When working with the preview, consider the following:
+ New Amazon Redshift clusters must be created with the **PREVIEW\_2020** maintenance track\. For more information about preview tracks, see [Choosing cluster maintenance tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks)\.
+ This feature is currently available for test purposes only\. Don't use the feature for production use cases\.
+ You can't switch an existing Amazon Redshift cluster from the current or trailing state to this preview track, or vice versa\. However, you can restore data from a cluster snapshot running on the current or trailing track\.
+  The data sharing preview period is expected to run until January 29, 2021\. Unless the preview period is extended, clusters should be deleted by January 29, 2021\. Clusters remaining on this track will be automatically deleted\.
+ You can use a cluster in any AWS Regions with Amazon Redshift RA3 instance types to preview the data sharing feature\.
+ For any questions, issues, or feedback related to the preview features during the preview period, email `redshift-datasharing@amazon.com` or open a support case with AWS Support\. 