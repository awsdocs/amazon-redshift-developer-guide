# Limitations<a name="limitations-super"></a>


|  | 
| --- |
|  This is prerelease documentation for SUPER data type fields larger than 1 MB, which are in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. Public preview will end on February 28, 2023\. Preview clusters and preview serverless workgroups and namespaces will be removed automatically two weeks after the end of the preview\. For preview terms and conditions, see Betas and Previews in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

**Note**  
You can create an Amazon Redshift cluster in **Preview** to test new features of Amazon Redshift\. You can't use those features in production or move your **Preview** cluster to a production cluster or a cluster on another track\. For preview terms and conditions, see *Beta and Previews* in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.  
Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
On the navigation menu, choose **Provisioned clusters dashboard**, and choose **Clusters**\. The clusters for your account in the current AWS Region are listed\. A subset of properties of each cluster is displayed in columns in the list\.
A banner displays on the **Clusters** list page that introduces preview\. Choose the button **Create preview cluster** to open the create cluster page\.
Enter properties for your cluster\. Choose the **Preview track** that contains the features you want to test\. We recommend entering a name for the cluster that indicates that it is on a preview track\. Choose options for your cluster, including options labeled as **\-preview**, for the features you want to test\. For general information about creating clusters, see [Creating a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#create-cluster) in the *Amazon Redshift Management Guide*\.
Choose **Create preview cluster** to create a cluster in preview\.
When your preview cluster is available, use your SQL client to load and query data\.
Your cluster must be created with the preview track named: preview\_2022\.  
You can create an Amazon Redshift Serverless workgroup in **Preview** to test new features of Amazon Redshift Serverless\. You can't use those features in production or move your **Preview** workgroup to a production workgroup\. For preview terms and conditions, see *Beta and Previews* in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.  
Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
On the navigation menu, choose **Severless dashboard**, and choose **Workgroup configuration**\. The workgroups for your account in the current AWS Region are listed\. A subset of properties of each workgroup is displayed in columns in the list\.
A banner displays on the **Workgroups** list page that introduces preview\. Choose the button **Create preview workgroup** to open the create workgroup page\.
Enter properties for your workgroup\. We recommend entering a name for the workgroup that indicates that it is in preview\. Choose options for your workgroup, including options labeled as **\-preview**, for the features you want to test\. Continue through the pages to enter options for your workgroup and namespace\. For general information about creating workgroups, see [Creating a workgroup with a namespace](https://docs.aws.amazon.com/redshift/latest/mgmt/serverless-console-workgroups-create-workgroup-wizard.html) in the *Amazon Redshift Management Guide*\.
Choose **Create preview workgroup** to create a workgroup in preview\.
When your preview workgroup is available, use your SQL client to load and query data\.

When using the SUPER data type, consider the following limitations:
+ You can't define SUPER columns as either a distribution or sort key\.
+ An individual SUPER field or object can hold up to 16 MB of data\.
+ An individual value within a SUPER object is limited to the maximum length of the corresponding Amazon Redshift type\. For example, a single string value loaded to SUPER is limited to the maximum VARCHAR length of 65535 bytes\.
+ You can't perform partial update or transform operations on SUPER columns\.
+ You can't use the SUPER data type and its alias in right joins or full outer joins\.
+ The SUPER data type doesn't support XML as inbound or outbound serialization format\. 
+ In the FROM clause of a subquery \(that is correlated or not\) that references a table variable for unnesting, the query can only refer to its parent table and not other tables\.
+ Casting limitations

  SUPER values can be cast to and from other data types, with the following exceptions:
  + Amazon Redshift doesn't differentiate integers and decimals of scale 0\.
  + If the scale isn't zero, SUPER data type has the same behavior as other Amazon Redshift data types, except that Amazon Redshift converts SUPER\-related errors to null, as shown in the following example\.

    ```
    SELECT 5::bool;
     bool
    -------
     True
    (1 row)
    
    SELECT 5::decimal::bool;
    ERROR:  cannot cast type numeric to boolean
    
    SELECT 5::super::bool;
     bool
    -------
     True
    (1 row)
    
    SELECT 5.0::bool;
    ERROR:  cannot cast type numeric to boolean
    
    SELECT 5.0::super::bool;
     bool
    -------
    (1 row)
    ```
  + Amazon Redshift doesn't cast the date and time types to SUPER data type\. Amazon Redshift can only cast the date and time data types from SUPER data type, as shown in the following example\.

    ```
    SELECT o.o_orderdate FROM customer_orders_lineitem c,c.c_orders o;
      order_date
    ------------------
     "2001-09-08"
    (1 row)
    
    
    SELECT JSON_TYPEOF(o.o_orderdate) FROM customer_orders_lineitem c,c.c_orders o;
     json_typeof
    -----------------
     string
    (1 row)
    
    
    SELECT o.o_orderdate::date FROM customer_orders_lineitem c,c.c_orders o;
     order_date
    ----------------
     2001-09-08
    (1 row)
    
    
    --date/time cannot be cast to super 
    SELECT '2019-09-09'::date::super;
    ERROR:  cannot cast type date to super
    ```
  + Cast from non\-scalar values \(object and array\) to string returns NULL\. To properly serialize these non\-scalar values, don't cast them\. Instead, use `json_serialize` to cast non\-scalar values\. The `json_serialize` function returns a varchar\. Typically, you don't need to cast non\-scalar values to varchar since Amazon Redshift implicitly serializes as shown in the following first example\. 

    ```
    SELECT r_nations FROM region_nations WHERE r_regionkey=300;
       r_nations
    ----------------
     [1,"abc",null]
    (1 row)
    
    SELECT r_nations::varchar FROM region_nations WHERE r_regionkey=300;
     r_nations
    -------------
    (1 row)
    
    SELECT JSON_SERIALIZE(r_nations) FROM region_nations WHERE r_regionkey=300;
     json_serialize
    -----------------
     [1,"abc",null]
    (1 row)
    ```
  + For case\-insensitive databases, Amazon Redshift doesn't support the SUPER data type\. For case\-insensitive columns, Amazon Redshift doesn't cast them to the SUPER type\. Thus, Amazon Redshift doesn't support SUPER columns interacting with case\-insensitive columns that trigger casting\.
+ Amazon Redshift doesn't support volatile functions, such as RANDOM \( \) or TIMEOFDAY \( \), in subqueries that unnest an outer table or a left\-hand side \(LHS\) of IN functions with such subqueries\.
+ Amazon Redshift doesn't support the following query functionality on tables with SUPER type columns that hold any SUPER object larger than 1 MB\. 
  + Cross\-functional limitations
    +  Data sharing 
    +  Concurrency scaling 
    +  Query federation 
  +  Core workflow limitations 
    +  Elastic resize and cross instance restore 
  +  Ingestion limitations 
    +  Parquet and JSON are the only supported file formats for using the COPY command on tables holding 16 MB SUPER type objects\. 
  +  Query limitations 
    +  Distinct aggregates on queries that include columns holding SUPER data 
    +  Python user\-defined functions \(UDFs\) that access columns holding SUPER data 
    +  Recursive common table expressions \(CTEs\) on tables with columns holding SUPER data 