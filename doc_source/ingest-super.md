# Loading semistructured data into Amazon Redshift<a name="ingest-super"></a>

Use the SUPER data type to persist and query hierarchical and generic data in Amazon Redshift\. Amazon Redshift introduces the `json_parse` function to parse data in JSON format and convert it into the SUPER representation\. Amazon Redshift also supports loading a JSON document without shredding the attributes of its JSON structures into multiple columns\.

The default encoding for SUPER data type is LZO\. 

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

## Using COPY to load JSON data in Amazon Redshift<a name="copy_json"></a>

Use the COPY command to load data formatted as JSON, ORC, and Parquet into SUPER data columns\. Semistructured data support in Amazon Redshift enables you to load a JSON document without shredding the attributes of its JSON structures into multiple columns\. Amazon Redshift provides two methods to ingest JSON document using the COPY even with JSON structure that is fully unknown or partially unknown: 
+ Store the data deriving from a JSON document into a single SUPER data column\. This method is useful when the schema isn't known or is expected to change\. Thus it is easier to store the entire tuple in a single SUPER column\.
+ Shred the JSON document into multiple Amazon Redshift columns\. Attributes can be Amazon Redshift scalars or SUPER values\. 

**Copying JSON document into a single SUPER data column**

To copy a JSON document into a single SUPER data column, create a table with a single SUPER data column\.

```
CREATE TABLE region_nations_noshred (rdata SUPER);
```

Copy the data from Amazon S3 into the single SUPER data column\. Specify the `noshred` option in the FORMAT JSON clause to ingest the JSON source data into a single SUPER data column\.

```
COPY region_nations_noshred FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 'noshred';
```

After the COPY has successfully ingested the JSON, the customers table has a cdata SUPER data column that contains the data of the entire JSON object\. The ingested data maintains all the properties of the JSON hierarchy but the leaves are converted to Amazon Redshift scalar types for efficient query processing\.

Use the following query to retrieve the original JSON string with the following query:

```
SELECT rdata FROM region_nations_noshred;
```

Furthermore, when Amazon Redshift generates a SUPER data column, it becomes accessible using JDBC as a string through JSON serialization\. For more information, see [Serializing complex nested JSON](serializing-complex-JSON.md)\.

**Copying JSON document into multiple SUPER data columns**

You can shred a JSON document into multiple columns that can be SUPER data columns or Amazon Redshift scalar types\. Amazon Redshift spreads different portions of the JSON object to different columns\.

```
CREATE TABLE region_nations
(
 r_regionkey smallint
 ,r_name varchar
 ,r_comment varchar
 ,r_nations super
 );
```

In order to COPY the data of the previous example into this table, run a COPY command with the AUTO option with FORMAT JSON to split the JSON value across multiple columns\. The COPY matches the JSON attributes with column names and allows nested values, such as JSON arrays and objects, to be ingested as SUPER values\. 

```
COPY region_nations FROM 's3://redshift-downloads/semistructured/tpch-nested/data/json/region_nation'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::xxxxxxxxxxxx:role/Redshift-S3'
FORMAT JSON 'auto';
```

Use the following query to access the table that shows data spread to multiple columns\. The SUPER data columns are printed using the JSON format\.

```
SELECT r_regionkey,r_name,r_comment,r_nations[0].n_nationkey FROM region_nations ORDER BY 1,2,3 LIMIT 1;
```

**Copying data from columnar format Parquet and ORC**

If your semistructured or nested data is already available in either Apache Parquet or Apache ORC formats, then you can use the COPY command to ingest data into Amazon Redshift\. The Amazon Redshift table structure should match the number of columns and the column data types of the Parquet or ORC files\. By specifying SERIALIZETOJSON in the COPY command, any column type in the file that aligns with a SUPER column in the table can be loaded as SUPER, including structure and array types\.

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