# Electric vehicle station\-data tutorial \(preview\)<a name="materialized-view-streaming-ingestion-example-station-data"></a>


|  | 
| --- |
| The following is documentation for the public preview of Amazon Redshift streaming ingestion, which consumes data from Kinesis Data Streams\. Preview features are built and tested in Redshift, but test coverage of all use cases may not be complete\. This documentation and the feature are both subject to change until it is released for general availability\. We recommend that you use this feature only with test clusters, and not in production\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

This procedure demonstrates how to ingest data from a Kinesis stream named *ev\_station\_data*, which contains consumption data from different EV charging stations, in JSON format\. The schema is well defined\. The example shows how to store the data as raw JSON and also how to convert the JSON data to Amazon Redshift data types as it's ingested\.

**Producer setup**

1. Using Amazon Kinesis Data Streams, follow the steps to create a stream named `ev_station_data`\. Choose **On\-demand** for the **Capacity mode**\. For more information, see [Creating a Stream via the AWS Management Console](https://docs.aws.amazon.com/streams/latest/dev/how-do-i-create-a-stream.html)\.

1. The [Amazon Kinesis Data Generator](https://awslabs.github.io/amazon-kinesis-data-generator/web/producer.html?) can help you generate test data for use with your stream\. Follow the steps detailed in the tool to get started, and use the following data template for generating your data:

   ```
   {
       
      "_id" : "{{random.uuid}}",
      "clusterID": "{{random.number(
           {   "min":1,
               "max":50
           }
       )}}", 
       "connectionTime": "{{date.now("YYYY-MM-DD HH:mm:ss")}}",
       "kWhDelivered": "{{commerce.price}}",
       "stationID": "{{random.number(
           {   "min":1,
               "max":467
           }
       )}}",
         "spaceID": "{{random.word}}-{{random.number(
           {   "min":1,
               "max":20
           }
       )}}",
    
      "timezone": "America/Los_Angeles",
      "userID": "{{random.number(
           {   "min":1000,
               "max":500000
           }
       )}}"
   }
   ```

    Each JSON object in the stream data has the following properties: 

   ```
   {
       "_id": "12084f2f-fc41-41fb-a218-8cc1ac6146eb",
       "clusterID": "49",
       "connectionTime": "2022-01-31 13:17:15",
       "kWhDelivered": "74.00",
       "stationID": "421",
       "spaceID": "technologies-2",
       "timezone": "America/Los_Angeles",
       "userID": "482329"
   }
   ```

**Amazon Redshift setup**

These steps show you how to configure the materialized view to ingest data\.

1. Create an external schema to map the data from Kinesis to a Redshift object\.

   ```
   CREATE EXTERNAL SCHEMA evdata FROM KINESIS
   IAM_ROLE 'arn:aws:iam::0123456789:role/redshift-streaming-role';
   ```

   For information about how to configure the IAM role, see [Getting started with streaming ingestion](materialized-view-streaming-ingestion.md#materialized-view-streaming-ingestion-getting-started)\.

1. Create a materialized view to consume the stream data\. The following examples show both methods of defining materialized views to ingest the JSON source data\.

   First, store stream records in semi\-structured SUPER format\. In this example, the JSON source is stored in Redshift without converting to Redshift types\.

   ```
   CREATE MATERIALIZED VIEW ev_station_data AS
       SELECT approximatearrivaltimestamp,
       partitionkey,
       shardid,
       sequencenumber,
       json_parse(from_varbyte(data, 'utf-8')) as payload    
       FROM evdata."ev_station_data";
   ```

    In contrast, in the following materialized view definition, the materialized view has a defined schema in Redshift\. The materialized view is distributed on the UUID value from the stream and is sorted by the `approximatearrivaltimestamp` value\.

   ```
   CREATE MATERIALIZED VIEW ev_station_data_extract DISTKEY(5) sortkey(1) AS
       SELECT approximatearrivaltimestamp,
       partitionkey,
       shardid,
       sequencenumber,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'_id')::character(36) as ID,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'clusterID')::varchar(30) as clusterID,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'connectionTime')::varchar(20) as connectionTime,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'kWhDelivered')::DECIMAL(10,2) as kWhDelivered,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'stationID')::DECIMAL(10,2) as stationID,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'spaceID')::varchar(100) as spaceID,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'timezone')::varchar(30) as timezone,
       json_extract_path_text(from_varbyte(data, 'utf-8'),'userID')::varchar(30) as userID
       FROM evdata."ev_station_data";
   ```

1. Refresh the materialized view\.

   ```
   REFRESH MATERIALIZED VIEW ev_station_data_extract;
   ```

**Query the stream**

1. Query the refreshed materialized view to get usage statistics\.

   ```
   SELECT to_timestamp(connectionTime, 'YYYY-MM-DD HH24:MI:SS') as connectiontime
   ,SUM(kWhDelivered) AS Energy_Consumed 
   ,count(distinct userID) AS #Users
   from ev_station_data_extract
   group by  to_timestamp(connectionTime, 'YYYY-MM-DD HH24:MI:SS')
   order by 1 desc;
   ```

1. View results\.

   ```
   connectiontime	        energy_consumed	#users
   2022-02-08 16:07:21+00	  4139	        10
   2022-02-08 16:07:20+00	  5571	        10
   2022-02-08 16:07:19+00	  8697	        20
   2022-02-08 16:07:18+00	  4408	        10
   2022-02-08 16:07:17+00	  4257	        10
   2022-02-08 16:07:16+00	  6861	        10
   2022-02-08 16:07:15+00	  5643	        10
   2022-02-08 16:07:14+00	  3677	        10
   2022-02-08 16:07:13+00	  4673	        10
   2022-02-08 16:07:11+00	  9689	        20
   ```