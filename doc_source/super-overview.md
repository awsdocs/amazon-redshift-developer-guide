# Ingesting and querying semistructured data in Amazon Redshift \(preview\)<a name="super-overview"></a>


|  | 
| --- |
| This is prerelease documentation for the semistructured data feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

By using *semistructured data support* in Amazon Redshift, you can ingest and store semistructured data in your Amazon Redshift data warehouses\. Using the SUPER data type and PartiQL language, Amazon Redshift expands data warehouse capability to integrate with both SQL and NoSQL data sources\. This way, Amazon Redshift enables efficient analytics on relational and semistructured stored data such as JSON\.

Amazon Redshift offers two forms of semistructured data support: the SUPER data type and Amazon Redshift Spectrum\. 

Use the SUPER data type if you need to insert or update small batches of JSON data with low latency\. Also, use SUPER when your query requires strong consistency, predictable query performance, complex query support, and ease of use with evolving schemas and schemaless data\. 

In contrast, use Amazon Redshift Spectrum with an open file format if your data query requires integration with other AWS services and with data mainly stored in Amazon S3 for archival purposes\.

## Use cases for the SUPER data type<a name="super-overview-use-cases"></a>

Semistructured data support using the SUPER data type in Amazon Redshift provides superior performance, flexibility, and ease of use\. The following use cases help demonstrate how you can use semistructured data support with SUPER\.

**Rapid and flexible insertion of JSON data** – Amazon Redshift supports rapid transactions that can parse JSON and store it as a SUPER value\. The insert transactions can operate up to five times faster than performing the same insertions into tables that have shredded the attributes of SUPER into conventional columns\. For example, suppose that the incoming JSON is of the form \{“a”:\.\., “b”:\.\., “c”“\.\.\., \.\.\.\}\. You can accelerate the insert performance many times by storing the incoming JSON into a table TJ with a single SUPER column S, instead of storing it into a conventional table TR with columns ”a’, ‘b’, “c’, and so on\. When there are hundreds of attributes in the JSON, the performance advantage of SUPER data type becomes substantial\.

Also, SUPER data type doesn't need a regular schema\. You don't need to introspect and clean up the incoming JSON before storing it\. For example, suppose an incoming JSON has a string “c” attribute and others that have an integer “c” attribute, without the SUPER data type\. In this case, you have to either separate c\_string and c\_int columns or clean up the data\. In contrast, with the SUPER data type, all JSON data is stored during ingestion without the loss of information\. Later, you can use the PartiQL extension of SQL to analyze the information\.

**Flexible queries for discovery** – After you have stored your semistructured data \(such as JSON\) into a SUPER data value, you can query it without imposing a schema\. You can use PartiQL dynamic typing and lax semantics to run your queries and discover the deeply nested data you need, without the need to impose a schema before query\. 

**Flexible queries for extract, load, transform \(ETL\) operations into conventional materialized views** – After you have stored your schemaless and semistructured data into SUPER, you can use PartiQL materialized views to introspect the data and shred them into materialized views\.

The materialized views with the shredded data are a good example of performance and usability advantages to your classic analytics cases\. When you perform analytics on the shredded data, the columnar organization of Amazon Redshift materialized views provides better performance\. Furthermore, users and business intelligence \(BI\) tools that require a conventional schema for ingested data can use views \(either materialized or virtual\) as the conventional schema presentation of the data\.

After your PartiQL materialized views have extracted the data found in JSON or SUPER into conventional columnar materialized views, you can query the materialized views\. For more information on how the SUPER data type works with materialized views, see [Using SUPER data type with materialized views](r_SUPER_MV.md)\.

## Concepts for SUPER data type use<a name="super-overview-concepts"></a>

Following, you can find some Amazon Redshift SUPER data type concepts\.

**Understand what the SUPER data type is in Amazon Redshift** – The *SUPER* data type is an Amazon Redshift data type that enables the storage of schemaless arrays and structures that contain Amazon Redshift scalars and possibly nested arrays and structures\. The SUPER data type can natively store different formats of semistructured data, such as JSON or data originating from document\-oriented sources\. You can add a new SUPER column to store semistructured data and write queries that access the SUPER column, along with the usual scalar columns\. For more information about the SUPER data type, see [SUPER type](r_SUPER_type.md)\.

**Ingest schemaless JSON into SUPER** – With the flexible semistructured SUPER data type, Amazon Redshift can receive and ingest schemaless JSON into a SUPER value\. For example, Amazon Redshift can ingest the JSON value \[10\.5, "first"\] into a SUPER value \[10\.5, ‘first’\], that is an array containing the Amazon Redshift decimal 10\.5 and varchar ‘first’\. Amazon Redshift can ingest the JSON into a SUPER value using the COPY command or the JSON parse function, such as *json\_parse\('\[10\.5, "first"\]'\)*\. Both COPY and JSON\_PARSE ingest JSON using strict parsing semantics by default\. You can also construct SUPER values including arrays and structures, using the database data themselves\.

The SUPER column requires no schema modifications while ingesting the irregular structures of schemaless JSON\. For example, while analyzing a click\-stream, you initially store in the SUPER column “click” structures with attributes “IP” and “time”\. You can add an attribute “customer id” without changing your schema in order to ingest such changes\.

The native format used for the SUPER data type is a binary format that requires lesser space than the JSON value in its textual form\. This enables faster ingestion and runtime processing of SUPER values at query time\. 

**Query SUPER data with PartiQL** – PartiQL is a backward\-compatible extension of SQL\-92 that many AWS services currently use\. With the use of PartiQL, familiar SQL constructs seamlessly combine access to both the classic, tabular SQL data and the semistructured data of SUPER\. You can perform object and array navigation and unnest arrays\. PartiQL extends the standard SQL language to declaratively express and process nested and multivalued data\.

PartiQL is an extension of SQL where the nested and schemaless data of SUPER columns are first\-class citizens\. PartiQL doesn't require all query expressions to be type\-checked during query compilation time\. This approach enables query expressions that contain the SUPER data type to be dynamically typed during query execution when the actual types of the data inside the SUPER columns are accessed\. Also, PartiQL operates in a lax mode where type inconsistencies don't cause failures\. The combination of schemaless and lax query processing makes PartiQL ideal for extract, load, transfer \(ELT\) applications where your SQL query evaluates the JSON data that are ingested in the SUPER columns\.

**Integrate with Redshift Spectrum** – Amazon Redshift supports multiple aspects of PartiQL when running Redshift Spectrum queries over JSON, Parquet, and other formats that have nested data\. Redshift Spectrum only supports nested data that has schemas\. For example, with Redshift Spectrum you can declare that your JSON data have an attribute *nested\_schemaful\_example* in a schema *ARRAY<STRUCT<a:INTEGER, b:DECIMAL\(5,2\)>>*\. The schema of this attribute determines that the data always contains an array, which contains a structure with integer *a* and decimal *b*\. If the data changes to include more attributes, the type also changes\. In contrast, the SUPER data type requires no schema\. You can store arrays with structure elements that have different attributes or types\. Also, some values can be stored outside arrays\. 

## Considerations for SUPER data use<a name="super-overview-considerations"></a>

When working with the preview, be aware of the following:
+ Amazon Redshift clusters must be on the **SQL\_PREVIEW** maintenance track\.
+ You can create a new Amazon Redshift cluster from the **SQL\_PREVIEW** track or restore a snapshot from the current track to the **SQL\_PREVIEW** track\.
+ You can't switch an existing Amazon Redshift cluster from the current or trailing state to this preview track, or vice versa\.
+ The semistructured data preview period is expected to run until February 28, 2021 and possibly later\. 
+ You can use a cluster in any AWS Region to preview the semistructured data feature using the **SQL\_PREVIEW** track\. For any questions, issues, or feedback related to the preview features during the preview period, email `redshift-super@amazon.com`\.
+ Use JDBC driver version 1\.2\.50 and ODBC driver version 1\.4\.17 or later for this preview\.
+ Find the schema examples used in this document at [SUPER sample dataset](r_SUPER_sample_dataset.md)\.
+ All the SQLs including DDL and COPY used in this doc and copy of selective TPCH modified queries that work with SUPER are included in the same S3 prefix for download\. To view or download the sql files, do one of the following:
  + Download the [SUPER tutorial SQL file](https://s3.amazonaws.com/redshift-downloads/semistructured/tutorialscripts/semistructured-tutorial.sql) and [TPCH file](https://s3.amazonaws.com/redshift-downloads/semistructured/tutorialscripts/super_tpch_queries.sql)\.
  + Using the AWS Amazon S3 CLI, run the following command\. You can use your own target path\.

    ```
    aws s3 cp s3://redshift-downloads/semistructured/tutorialscripts/semistructured-tutorial.sql /target/path
    aws s3 cp s3://redshift-downloads/semistructured/tutorialscripts/super_tpch_queries.sql /target/path
    ```