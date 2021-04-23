# Loading semistructured data into Amazon Redshift<a name="ingest-super"></a>

Use the SUPER data type to persist and query hierarchical and generic data in Amazon Redshift\. Amazon Redshift introduces the `json_parse` function to parse data in JSON format and convert it into the SUPER representation\. Amazon Redshift also supports loading SUPER columns using the COPY command\. The supported file formats are JSON, Avro, text, comma\-separated value \(CSV\) format, Parquet, and ORC\.

The default encoding for SUPER data type is ZSTD\. 

## Parsing JSON documents to SUPER columns<a name="parse_json"></a>

You can insert or update JSON data into a SUPER column using the JSON\_PARSE function\. The function parses data in JSON format and converts it into the SUPER data type, which you can use in INSERT or UPDATE statements\. 

The following example inserts JSON data into a SUPER column\. If the JSON\_PARSE function is missing in the query, Amazon Redshift treats the value as a single string instead of a JSON\-formatted string that must be parsed\.

If you update a SUPER data column, Amazon Redshift requires the complete document to be passed to column values\. Amazon Redshift doesn't support partial update\. 

```
INSERT INTO region_nations VALUES(0,
   'lar deposits. blithely final packages cajole. regular waters are final requests. regular accounts are according to',
   'AFRICA',
   JSON_PARSE('{"r_nations":[
      {"n_comment":" haggle. carefully final deposits detect slyly agai",
         "n_nationkey":0,
         "n_name":"ALGERIA"
      },
      {"n_comment":"ven packages wake quickly. regu",
         "n_nationkey":5,
         "n_name":"ETHIOPIA"
      },
      {"n_comment":" pending excuses haggle furiously deposits. pending, express pinto beans wake fluffily past t",
         "n_nationkey":14,
         "n_name":"KENYA"
      },
      {"n_comment":"rns. blithely bold courts among the closely regular packages use furiously bold platelets?",
         "n_nationkey":15,
         "n_name":"MOROCCO"
      },
      {"n_comment":"s. ironic, unusual asymptotes wake blithely r",
         "n_nationkey":16,
         "n_name":"MOZAMBIQUE"
      }
   ]
}'));
```

## Using COPY to load SUPER columns in Amazon Redshift<a name="copy_json"></a>

In the following sections, you can learn about different ways to use the COPY command to load JSON data into Amazon Redshift\.

### Copying data from JSON and Avro<a name="copy_json-from-JSON"></a>

By using semistructured data support in Amazon Redshift, you can load a JSON document without shredding the attributes of its JSON structures into multiple columns\. 

Amazon Redshift provides two methods to ingest JSON document using COPY, even with a JSON structure that is fully or partially unknown: 

1. Store the data deriving from a JSON document into a single SUPER data column using the `noshred` option\. This method is useful when the schema isn't known or is expected to change\. Thus, this method makes it easier to store the entire tuple in a single SUPER column\.

1. Shred the JSON document into multiple Amazon Redshift columns using the `auto` or `jsonpaths` option\. Attributes can be Amazon Redshift scalars or SUPER values\. 

You can use these options with the JSON or Avro formats\.

The maximum size for a JSON object before shredding is 4 MB\.

### Copying a JSON document into a single SUPER data column<a name="copy_json-one-column"></a>

To copy a JSON document into a single SUPER data column, create a table with a single SUPER data column\.

```
CREATE TABLE region_nations_noshred (rdata SUPER);
```

Copy the data from Amazon S3 into the single SUPER data column\. To ingest the JSON source data into a single SUPER data column, specify the `noshred` option in the FORMAT JSON clause\.

```
COPY region_nations_noshred FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 'noshred';
```

After COPY has successfully ingested the JSON, your table has a `rdata` SUPER data column that contains the data of the entire JSON object\. The ingested data maintains all the properties of the JSON hierarchy\. However, the leaves are converted to Amazon Redshift scalar types for efficient query processing\.

Use the following query to retrieve the original JSON string\.

```
SELECT rdata FROM region_nations_noshred;
```

When Amazon Redshift generates a SUPER data column, it becomes accessible using JDBC as a string through JSON serialization\. For more information, see [Serializing complex nested JSON](serializing-complex-JSON.md)\.

### Copying a JSON document into multiple SUPER data columns<a name="copy_json-multiple-columns"></a>

You can shred a JSON document into multiple columns that can be either SUPER data columns or Amazon Redshift scalar types\. Amazon Redshift spreads different portions of the JSON object to different columns\.

```
CREATE TABLE region_nations
(
 r_regionkey smallint
 ,r_name varchar
 ,r_comment varchar
 ,r_nations super
 );
```

To copy the data of the previous example into the table, specify the AUTO option in the FORMAT JSON clause to split the JSON value across multiple columns\. COPY matches the top\-level JSON attributes with column names and allows nested values to be ingested as SUPER values, such as JSON arrays and objects\. 

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 'auto';
```

In some cases, there is a mismatch between column names and JSON attributes or the attribute to load is nested more than a level deep\. If so, use a `jsonpaths` file to manually map JSON attributes to Amazon Redshift columns\.

```
CREATE TABLE nations
(
 regionkey smallint
 ,name varchar
 ,comment super
 ,nations super
 );
```

Suppose that you want to load data to a table where the column names don't match the JSON attributes\. In the following example, the `nations` table is such a table\. You can create a `jsonpaths` file that maps the paths of attributes to the table columns by their position in the `jsonpaths` array\.

```
{"jsonpaths": [
       "$.r_regionkey",
       "$.r_name",
       "$.r_comment",
       "$.r_nations
    ]
}
```

The location of the `jsonpaths` file is used as the argument to FORMAT JSON\.

```
COPY nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 's3://redshift-downloads/semistructured/tpch-nested/data/jsonpaths/nations_jsonpaths.json';
```

Use the following query to access the table that shows data spread to multiple columns\. The SUPER data columns are printed using the JSON format\.

```
SELECT r_regionkey,r_name,r_comment,r_nations[0].n_nationkey FROM region_nations ORDER BY 1,2,3 LIMIT 1;
```

### Copying data from text and CSV<a name="copy_json-from-text-csv"></a>

Amazon Redshift represents SUPER columns in text and CSV formats as single\-line JSON objects\. The double quotation marks used for escaping in CSV require no intervention from users\. However, for text format, when the chosen delimiter might also appear in a SUPER field, use the ESCAPE option during COPY and UNLOAD\. 

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/csv/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT CSV;
```

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/text/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
DELIMITER ','
ESCAPE;
```

### Copying data from columnar\-format Parquet and ORC<a name="copy_json-from-parquet-orc"></a>

If your semistructured or nested data is already available in either Apache Parquet or Apache ORC format, you can use the COPY command to ingest data into Amazon Redshift\. 

The Amazon Redshift table structure should match the number of columns and the column data types of the Parquet or ORC files\. By specifying SERIALIZETOJSON in the COPY command, you can load any column type in the file that aligns with a SUPER column in the table as SUPER\. This includes structure and array types\.

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/parquet/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT PARQUET SERIALIZETOJSON;
```

The following example uses an ORC format\.

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/orc/region_nation'
IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT ORC SERIALIZETOJSON;
```

When the attributes of the date or time data types are in ORC, Amazon Redshift converts them to varchar upon encoding them in SUPER\.