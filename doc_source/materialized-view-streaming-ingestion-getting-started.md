# Getting started with streaming ingestion from Amazon Kinesis Data Streams<a name="materialized-view-streaming-ingestion-getting-started"></a>

 Setting up Amazon Redshift streaming ingestion involves creating an external schema that maps to the streaming data source and creating a materialized view that references the external schema\. Amazon Redshift streaming ingestion supports Kinesis Data Streams as a source\. As such, you must have a Kinesis Data Streams source available before configuring streaming ingestion\. If you don't have a source, follow the instructions in the Kinesis documentation at [Getting Started with Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/getting-started.html) or create one on the console using the instructions at [Creating a Stream via the AWS Management Console](https://docs.aws.amazon.com/streams/latest/dev/how-do-i-create-a-stream.html)\.

 Amazon Redshift streaming ingestion uses a materialized view, which is updated directly from the stream when `REFRESH` is run\. The materialized view maps to the stream data source\. You can perform filtering and aggregations on the stream data as part of the materialized\-view definition\. Your streaming ingestion materialized view \(the *base* materialized view\) can reference only one stream, but you can create additional materialized views that join with the base materialized view and with other materialized views or tables\. 

**Note**  
*Streaming ingestion and Amazon Redshift Serverless* \- The configuration steps in this topic apply both to provisioned Amazon Redshift clusters and to Amazon Redshift Serverless\. For more information, see [Streaming ingestion considerations](materialized-view-streaming-ingestion.md#materialized-view-streaming-ingestion-considerations)\.

Assuming you have a Kinesis Data Streams stream available, the first step is to define a schema in Amazon Redshift with `CREATE EXTERNAL SCHEMA` and to reference a Kinesis Data Streams resource\. Following that, to access data in the stream, define the `STREAM` in a materialized view\. You can store stream records in the semi\-structured `SUPER` format, or define a schema that results in data converted to Redshift data types\. When you query the materialized view, the returned records are a point\-in\-time view of the stream\. 

1. Create an IAM role with a trust policy that allows your Amazon Redshift cluster to assume the role\. For information about how to configure the trust policy for the IAM role, see [Authorizing Amazon Redshift to access other AWS services on your behalf](https://docs.aws.amazon.com/redshift/latest/mgmt/authorizing-redshift-service.html)\. After it is created, the role should have the following IAM policy, which provides permission for communication with the Amazon Kinesis data stream\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ReadStream",
               "Effect": "Allow",
               "Action": [
                   "kinesis:DescribeStreamSummary",
                   "kinesis:GetShardIterator",
                   "kinesis:GetRecords",
                   "kinesis:DescribeStream"
               ],
               "Resource": "arn:aws:kinesis:*:0123456789:stream/*"
           },
           {
               "Sid": "ListStream",
               "Effect": "Allow",
               "Action": [
                   "kinesis:ListStreams",
                   "kinesis:ListShards"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. Check your VPC and verify that your Amazon Redshift cluster has a route to get to the Kinesis Data Streams endpoints over the internet using a NAT gateway or internet gateway\. If you want traffic between your Amazon Redshift cluster and Kinesis Data Streams to remain within the AWS network, consider using a Kinesis Interface VPC Endpoint\. For more information, see [Using Amazon Kinesis Data Streams Kinesis Data Streams with Interface VPC Endpoints](https://docs.aws.amazon.com/streams/latest/dev/vpc.html)\.

1. In Amazon Redshift, create an external schema to map the data from Kinesis to a schema\.

   ```
   CREATE EXTERNAL SCHEMA kds
   FROM KINESIS
   IAM_ROLE { default | 'iam-role-arn' };
   ```

    Streaming ingestion for Kinesis Data Streams doesn't require an authentication type\. It uses the IAM role defined in the `CREATE EXTERNAL SCHEMA` statement for making Kinesis Data Streams requests\.  

1. Create a materialized view to consume the stream data\. The following example defines a materialized view with JSON source data\. Note that the following view validates the data is valid JSON source \. Kinesis stream names are case sensitive and can contain both uppercase and lowercase letters\. To use case\-sensitive identifiers, you can set the configuration setting `enable_case_sensitive_identifier` to `true` at either the session or cluster level\. For more information, see [Names and identifiers](https://docs.aws.amazon.com/redshift/latest/dg/r_names.html) and [enable\_case\_sensitive\_identifier](https://docs.aws.amazon.com/redshift/latest/dg/r_enable_case_sensitive_identifier.html)\.

   ```
   CREATE MATERIALIZED VIEW my_view AUTO REFRESH YES AS
   SELECT approximate_arrival_timestamp,
   JSON_PARSE(kinesis_data) as Data
   FROM kds.my_stream_name
   WHERE CAN_JSON_PARSE(kinesis_data);
   ```

   To turn on auto refresh, use `AUTO REFRESH YES`\. The default behavior is manual refresh\. 

   Metadata columns include the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/materialized-view-streaming-ingestion-getting-started.html)

1. Refresh the view, which invokes Amazon Redshift to read from the stream and load data into the materialized view\.

   ```
   REFRESH MATERIALIZED VIEW my_view;
   ```

1. Query data in the materialized view\.

   ```
   select * from my_view;
   ```