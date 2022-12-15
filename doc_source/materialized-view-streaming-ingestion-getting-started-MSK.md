# Getting started with streaming ingestion from Amazon Managed Streaming for Apache Kafka<a name="materialized-view-streaming-ingestion-getting-started-MSK"></a>

 The purpose of Amazon Redshift streaming ingestion is to simplify the process for directly ingesting stream data from a streaming service into Amazon Redshift\. This works with Amazon MSK and Amazon MSK Serverless, and with Kinesis\. Amazon Redshift streaming ingestion removes the need to stage a Kinesis Data Streams stream or an Amazon MSK topic in Amazon S3 before ingesting the stream data into Amazon Redshift\.

 On a technical level, streaming ingestion, both from Amazon Kinesis Data Streams and Amazon Managed Streaming for Apache Kafka, provides low\-latency, high\-speed ingestion of stream or topic data into an Amazon Redshift materialized view\. Following setup, using materialized view refresh, you can take in large data volumes\. 

Set up Amazon Redshift streaming ingestion for Amazon MSK by performing the following steps:

1. Create an external schema that maps to the streaming data source\.

1. Create a materialized view that references the external schema\.

You must have an Amazon MSK source available, before configuring Amazon Redshift streaming ingestion\. If you do not have a source, follow the instructions at [Getting Started Using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/getting-started.html)\.

**Note**  
*Streaming ingestion and Amazon Redshift Serverless* \- The configuration steps in this topic apply to Amazon Redshift Serverless\. For more information, see [Streaming ingestion considerations](materialized-view-streaming-ingestion.md#materialized-view-streaming-ingestion-considerations)\.

## Setting up IAM and performing streaming ingestion from Kafka<a name="materialized-view-streaming-ingestion-getting-started-MSK-setup"></a>

Assuming you have an Amazon MSK cluster available, the first step is to define a schema in Amazon Redshift with `CREATE EXTERNAL SCHEMA` and to reference the Kafka topic as the data source\. Following that, to access data in the topic, define the `STREAM` in a materialized view\. You can store records from your topic in the semi\-structured `SUPER` format, or define a schema that results in data converted to Amazon Redshift data types\. When you query the materialized view, the returned records are a point\-in\-time view of the topic\.

1. Create an IAM role with a trust policy that allows your Amazon Redshift cluster to assume the role\. For information about how to configure the trust policy for the IAM role, see [Authorizing Amazon Redshift to access other AWS services on your behalf](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html)\. After it's created, the role should have the following IAM policy, which provides permission for communication with the Amazon MSK cluster\. The policy you need depends on the authentication method used on your cluster, if you use Amazon MSK\. See [Authentication and Authorization for Apache Kafka APIs](https://docs.aws.amazon.com/msk/latest/developerguide/kafka_apis_iam.html) for authentication methods available in Amazon MSK\.

   An IAM policy for Amazon MSK using unauthenticated access:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "VisualEditor0",
               "Effect": "Allow",
               "Action": [
                   "kafka:GetBootstrapBrokers"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   An IAM policy for Amazon MSK when using IAM authentication:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "MSKIAMpolicy",
               "Effect": "Allow",
               "Action": [
                   "kafka-cluster:ReadData",
                   "kafka-cluster:DescribeTopic",
                   "kafka-cluster:Connect"
               ],
               "Resource": [
                   "arn:aws:kafka:*:0123456789:cluster/*/*",
                   "arn:aws:kafka:*:0123456789:topic/*/*/*"
               ]
           },
           {
               "Sid": "MSKPolicy",
               "Effect": "Allow",
               "Action": [
                   "kafka:GetBootstrapBrokers"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1.  Check your VPC and verify that your Amazon Redshift cluster has a route to get to your Amazon MSK cluster\. The inbound security group rules for your Amazon MSK cluster should allow your Amazon Redshift cluster's security group\. The ports you specify depend on the authentication method used for your cluster, if you use Amazon MSK\. For more information, see [Port information](https://docs.aws.amazon.com/msk/latest/developerguide/port-info.html) and [Access from within AWS but outside the VPC](https://docs.aws.amazon.com/msk/latest/developerguide/aws-access.html)\.

1. Enable enhanced VPC routing on your Amazon Redshift cluster\. For more information, see [Enabling enhanced VPC routing](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-enabling-cluster.html)\.

1. In Amazon Redshift, create an external schema to map to the Amazon MSK cluster\. 

   ```
   CREATE EXTERNAL SCHEMA MySchema
   FROM MSK
   IAM_ROLE { default | 'iam-role-arn' }
   AUTHENTICATION { none | iam }
   CLUSTER_ARN 'msk-cluster-arn';
   ```

   Note in the `FROM` clause that Amazon MSK denotes that the schema maps data from Managed Kafka Services\.  `CLUSTER_ARN` specifies the Amazon MSK cluster that you’re streaming from\.

1. Create a materialized view to consume the data from the topic\. The following example defines a materialized view with JSON source data\. Note that the following view validates that the data is valid JSON and utf8\. Kafka topic names are case sensitive and can contain both uppercase and lowercase letters\. To use case\-sensitive identifiers, you can set the configuration `enable_case_sensitive_identifier` to `true` at either the session or cluster level\. For more information, see [Names and identifiers](https://docs.aws.amazon.com/redshift/latest/dg/r_names.html) and [r\_enable\_case\_sensitive\_identifier](https://docs.aws.amazon.com/redshift/latest/dg/r_enable_case_sensitive_identifier.html)\.

   ```
   CREATE MATERIALIZED VIEW MyView AS
   SELECT "kafka_partition", 
    "kafka_offset", 
    "kafka_timestamp_type", 
    "kafka_timestamp", 
    "kafka_key", 
    JSON_PARSE("kafkavalue") as Data, 
    "kafka_headers"
   FROM MySchema.MyTopic;
   ```

   Metadata columns include the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/materialized-view-streaming-ingestion-getting-started-MSK.html)

1. Refresh the view, which invokes Amazon Redshift to read from the topic and load data into the materialized view\.

   ```
   REFRESH MATERIALIZED VIEW my_view;
   ```

1. Query data in the materialized view\.

   ```
   select * from my_view;
   ```

   The materialized view is updated directly from the topic when `REFRESH` is run\. You create a materialized view that maps to the Kafka topic data source\. You can perform filtering and aggregations on the data as part of the materialized view definition\. Your streaming ingestion materialized view \(base materialized view\) can reference only one Kafka topic, but you can create additional materialized views that join with the base materialized view and with other materialized views or tables\.

For more information about limitations for streaming ingestion, see [Limitations](materialized-view-streaming-ingestion.md#materialized-view-streaming-ingestion-limitations)\.