# Streaming ingestion \(preview\)<a name="materialized-view-streaming-ingestion"></a>


|  | 
| --- |
| The following is documentation for the public preview of Amazon Redshift streaming ingestion, which consumes data from Kinesis Data Streams\. Preview features are built and tested in Redshift, but test coverage of all use cases may not be complete\. This documentation and the feature are both subject to change until it is released for general availability\. We recommend that you use this feature only with test clusters, and not in production\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

 Previously, loading data from a streaming service like Amazon Kinesis Streams into Amazon Redshift included several steps\. These included connecting the stream to an Amazon Kinesis Data Firehose and waiting for Kinesis Data Firehose to stage the data in Amazon S3, using various\-sized batches at varying\-length buffer intervals\. After this, Kinesis Data Firehose triggered a COPY command to load the data from Amazon S3 to a table in Redshift\. 

 Rather than including preliminary staging in Amazon S3, streaming ingestion provides low\-latency, high\-speed ingestion of stream data from [Kinesis Data Streams](https://aws.amazon.com/kinesis/data-streams/) into an Amazon Redshift materialized view\. Thus, it increases speed and reduces storage cost\. You can configure your Redshift cluster to enable streaming ingestion and create a materialized view, using SQL statements, as described in [Creating materialized views in Amazon Redshift](materialized-view-overview.md)\. After that, using materialized view refresh, you can ingest hundreds of megabytes of data per second from Amazon Kinesis Data Streams into Amazon Redshift\. This results in fast access to external data that is quickly refreshed\. 

## Data flow<a name="materialized-view-streaming-ingestion-data-flow"></a>

 Amazon Redshift is a stream consumer\. An Amazon Redshift materialized view is the landing area for data that is read from the stream, which is processed as it arrives\. For instance, JSON values can be consumed and mapped to Redshift data columns using SQL, which is familiar\. When the materialized view is refreshed, Redshift compute nodes allocate each data shard to a compute slice\. Each slice consumes data from the allocated shards until the view reaches parity with the stream\. In the same manner, subsequent materialized view refreshes read data from the last `SEQUENCE_NUMBER` of the previous refresh until it reaches parity with the stream data\. 

## Streaming ingestion use cases<a name="materialized-view-streaming-ingestion-use-cases"></a>

 Use cases for Amazon Redshift streaming ingestion center around working with data that is generated continually \(streamed\) and needs to be processed within a short period \(latency\) of its generation\. This is called near\-real\-time analytics\. Sources of data can vary from IOT devices, system telemetry, or clickstream data from a busy website\. 

## Getting started with streaming ingestion<a name="materialized-view-streaming-ingestion-getting-started"></a>

 Setting up Amazon Redshift streaming ingestion involves creating an external schema that maps to the streaming data source and creating a materialized view that references the external schema\. Amazon Redshift streaming ingestion supports Kinesis Data Streams as a source\. As such, you need to have a Kinesis Data Streams source available prior to configuring streaming ingestion\. If you do not have a source, follow the instructions in the Kinesis documentation at [Getting Started with Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/getting-started.html) or create one on the console using the instructions at [Creating a Stream via the AWS Management Console](https://docs.aws.amazon.com/streams/latest/dev/how-do-i-create-a-stream.html)\.

 Amazon Redshift streaming ingestion uses a materialized view, which is updated directly from the stream when `REFRESH` is run\. The materialized view maps to the stream data source\. You can perform filtering and aggregations on the stream data as part of the materialized\-view definition\. Your streaming ingestion materialized view \(the *base* materialized view\) can reference only one stream, but you can create additional materialized views that join with the base materialized view and with other materialized views or tables\. 

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

1. In Amazon Redshift, create an external schema\.

   ```
   CREATE EXTERNAL SCHEMA schema_one
   FROM KINESIS
   IAM_ROLE { default | 'iam-role-arn' };
   ```

1. Create a materialized view to consume the stream data\. The following example defines a materialized view with JSON source data\. Note that the following view validates the data is valid JSON source and utf8\. Kinesis stream names are case\-sensitive and can contain both uppercase and lowercase letters\. To use case\-sensitive identifiers, set `enable_case_sensitive_identifier` to `true` at either the session or cluster level\. For more information, see [Names and identifiers](https://docs.aws.amazon.com/redshift/latest/dg/r_names.html) and [enable\_case\_sensitive\_identifier](https://docs.aws.amazon.com/redshift/latest/dg/r_enable_case_sensitive_identifier.html)\.

   ```
   CREATE MATERIALIZED VIEW my_view AS
   SELECT ApproximateArrivalTimestamp,
          JSON_PARSE(from_varbyte(Data, 'utf-8')) as Data
   FROM schema_one.my_stream_name
   WHERE is_utf8(Data) AND is_valid_json(from_varbyte(Data, 'utf-8'));
   ```

1. Refresh the view, which triggers Amazon Redshift to read from the stream and load data into the materialized view\.

   ```
   REFRESH MATERIALIZED VIEW my_view;
   ```

1. Query data in the materialized view\.

   ```
   select * from my_view;
   ```

## Limitations<a name="materialized-view-streaming-ingestion-limitations"></a>
+ Refreshes are currently manual but can be automated using the query scheduler\. See [Scheduling a query on the Amazon Redshift console](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-schedule-query.html)\.
+ JOINs are not currently supported in materialized views created on a stream\. After creating a materialized view on your stream, you can create another materialized view that can be used to join your streaming materialized view to other materialized views, tables or views\.
+ The materialized view refresh must finish consuming data from the stream within an hour\. If the refresh exceeds an hour, the refresh fails due to credentials expiration\.
+ Redshift stream ingestion does not support parsing records that have been aggregated by the Kinesis Producer Library \([KPL Key Concepts \- Aggregation](https://docs.aws.amazon.com/kinesis/latest/dev/kinesis-kpl-concepts.html#kinesis-kpl-concepts-aggretation)\)\. The aggregated records are ingested, but are stored as binary protocol buffer data\. \(See [Protocol buffers](https://developers.google.com/protocol-buffers) for more information\.\) Depending on how you push data to Kinesis, you may need to disable this feature\. For example, if you are using Kinesis Data Analytics, you can disable it by adding `'sink.producer.aggregation-enabled' = 'false'` when creating your Kinesis sink table\. For more information, see [Amazon Kinesis Data Streams SQL Connector](https://nightlies.apache.org/flink/flink-docs-release-1.13/docs/connectors/table/kinesis/)\.
+ The initial refresh after creating the materialized view starts from the `TRIM_HORIZON` of the stream\.
+ The materialized view must be incrementally maintainable\. Full recompute is not possible, because Kinesis does not preserve stream history past 24 hours, by default\. You can set longer data retention in Kinesis, but this can result in more technical maintenance and cost\. For more information, see [REFRESH MATERIALIZED VIEW](https://docs.aws.amazon.com/redshift/latest/dg/materialized-view-refresh-sql-command.html)\.
+ While the cluster is paused, refreshes on the materialized view are not available until the cluster is resumed\. Records that expired from the stream during the paused period are not available\.
+ Data formats supported are limited to those that can be converted from `VARBYTE`\. For more information, see [VARBYTE type](r_VARBYTE_type.md)\.
+ `VARBYTE` does not currently support any decompression methods, so Kinesis records containing compressed data cannot be queried inside Amazon Redshift\. Instead, you should decompress your data before pushing it into the stream\.