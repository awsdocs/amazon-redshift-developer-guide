# Working with recommendations from Amazon Redshift Advisor<a name="advisor"></a>

To help you improve the performance and decrease the operating costs for your Amazon Redshift cluster, Amazon Redshift Advisor offers you specific recommendations about changes to make\. Advisor develops its customized recommendations by analyzing performance and usage metrics for your cluster\. These tailored recommendations relate to operations and cluster settings\. To help you prioritize your optimizations, Advisor ranks recommendations by order of impact\.

Advisor bases its recommendations on observations regarding performance statistics or operations data\. Advisor develops observations by running tests on your clusters to determine if a test value is within a specified range\. If the test result is outside of that range, Advisor generates an observation for your cluster\. At the same time, Advisor creates a recommendation about how to bring the observed value back into the best\-practice range\. Advisor only displays recommendations that should have a significant impact on performance and operations\. When Advisor determines that a recommendation has been addressed, it removes it from your recommendation list\. 

For example, suppose that your data warehouse contains a large number of uncompressed table columns\. In this case, you can save on cluster storage costs by rebuilding tables using the `ENCODE` parameter to specify column compression\. In another example, suppose that Advisor observes that your cluster contains a significant amount of data in uncompressed table data\. In this case, it provides you with the SQL code block to find the table columns that are candidates for compression and resources that describe how to compress those columns\. 

## Amazon Redshift Regions<a name="advisor-regions"></a>

The Amazon Redshift Advisor feature is available only in the following AWS Regions: 
+ US East \(N\. Virginia\) Region \(us\-east\-1\)
+ US West \(N\. California\) Region \(us\-west\-1\)
+ US West \(Oregon\) Region \(us\-west\-2\) 
+ Asia Pacific \(Seoul\) Region \(ap\-northeast\-2\)
+ Asia Pacific \(Singapore\) Region \(ap\-southeast\-1\)
+ Asia Pacific \(Sydney\) Region \(ap\-southeast\-2\)
+ Asia Pacific \(Tokyo\) Region \(ap\-northeast\-1\)
+ Europe \(Frankfurt\) Region \(eu\-central\-1\)
+ Europe \(Ireland\) Region \(eu\-west\-1\)

**Topics**
+ [Amazon Redshift Regions](#advisor-regions)
+ [Viewing Amazon Redshift Advisor recommendations on the console](access-advisor.md)
+ [Amazon Redshift Advisor recommendations](advisor-recommendations.md)