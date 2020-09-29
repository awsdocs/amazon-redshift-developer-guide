# Data format parameters<a name="copy-parameters-data-format"></a>

By default, the COPY command expects the source data to be character\-delimited UTF\-8 text\. The default delimiter is a pipe character \( \| \)\. If the source data is in another format, use the following parameters to specify the data format: 
+ [FORMAT](#copy-format)
+ [CSV](#copy-csv)
+ [DELIMITER](#copy-delimiter) 
+ [FIXEDWIDTH](#copy-fixedwidth) 
+ [SHAPEFILE](#copy-shapefile) 
+ [AVRO](#copy-avro) 
+ [JSON](#copy-json) 
+ [PARQUET](#copy-parquet) 
+ [ORC](#copy-orc) 

In addition to the standard data formats, COPY supports the following columnar data formats for COPY from Amazon S3: 
+ [ORC](#copy-orc) 
+ [PARQUET](#copy-parquet) 

COPY from columnar format is supported with certain restriction\. For more information, see [COPY from columnar data formats](copy-usage_notes-copy-from-columnar.md)\. <a name="copy-data-format-parameters"></a>Data format parameters

FORMAT \[AS\]  <a name="copy-format"></a>
\(Optional\) Identifies data format keywords\. The FORMAT arguments are described following\.

CSV \[ QUOTE \[AS\] *'quote\_character'* \]  <a name="copy-csv"></a>
Enables use of CSV format in the input data\. To automatically escape delimiters, newline characters, and carriage returns, enclose the field in the character specified by the QUOTE parameter\. The default quotation mark character is a double quotation mark \( " \)\. When the quotation mark character is used within a field, escape the character with an additional quotation mark character\. For example, if the quotation mark character is a double quotation mark, to insert the string `A "quoted" word` the input file should include the string `"A ""quoted"" word"`\. When the CSV parameter is used, the default delimiter is a comma \( , \)\. You can specify a different delimiter by using the DELIMITER parameter\.   
When a field is enclosed in quotation marks, white space between the delimiters and the quotation mark characters is ignored\. If the delimiter is a white space character, such as a tab, the delimiter isn't treated as white space\.  
CSV can't be used with FIXEDWIDTH, REMOVEQUOTES, or ESCAPE\.     
QUOTE \[AS\] *'quote\_character'*  <a name="copy-csv-quote"></a>
Optional\. Specifies the character to be used as the quotation mark character when using the CSV parameter\. The default is a double quotation mark \( " \)\. If you use the QUOTE parameter to define a quotation mark character other than double quotation mark, you don't need to escape double quotation marks within the field\. The QUOTE parameter can be used only with the CSV parameter\. The AS keyword is optional\.

DELIMITER \[AS\] \['*delimiter\_char*'\]   <a name="copy-delimiter"></a>
Specifies the single ASCII character that is used to separate fields in the input file, such as a pipe character \( \| \), a comma \( , \), or a tab \( \\t \)\. Non\-printing ASCII characters are supported\. ASCII characters can also be represented in octal, using the format '\\ddd', where 'd' is an octal digit \(0–7\)\. The default delimiter is a pipe character \( \| \), unless the CSV parameter is used, in which case the default delimiter is a comma \( , \)\. The AS keyword is optional\. DELIMITER can't be used with FIXEDWIDTH\.

FIXEDWIDTH '*fixedwidth\_spec*'   <a name="copy-fixedwidth"></a>
Loads the data from a file where each column width is a fixed length, rather than columns being separated by a delimiter\. The *fixedwidth\_spec* is a string that specifies a user\-defined column label and column width\. The column label can be either a text string or an integer, depending on what the user chooses\. The column label has no relation to the column name\. The order of the label/width pairs must match the order of the table columns exactly\. FIXEDWIDTH can't be used with CSV or DELIMITER\. In Amazon Redshift, the length of CHAR and VARCHAR columns is expressed in bytes, so be sure that the column width that you specify accommodates the binary length of multibyte characters when preparing the file to be loaded\. For more information, see [Character types](r_Character_types.md)\.   
The format for *fixedwidth\_spec* is shown following:   

```
'colLabel1:colWidth1,colLabel:colWidth2, ...'
```

SHAPEFILE \[ SIMPLIFY \[AUTO\] \[*'tolerance'*\] \]  <a name="copy-shapefile"></a>
Enables use of SHAPEFILE format in the input data\. By default, the first column of the shapefile is either a `GEOMETRY` or `IDENTITY` column\. All subsequent columns follow the order specified in the shapefile\.  
You can't use SHAPEFILE with FIXEDWIDTH, REMOVEQUOTES, or ESCAPE\.     
SIMPLIFY \[*tolerance*\]  <a name="copy-shapefile-simplify"></a>
\(Optional\) Simplifies all geometries during the ingestion process using the Ramer\-Douglas\-Peucker algorithm and the given tolerance\.   
SIMPLIFY AUTO \[*tolerance*\]  <a name="copy-shapefile-simplify"></a>
\(Optional\) Simplifies only geometries that are larger than the maximum geometry size\. This simplification uses the Ramer\-Douglas\-Peucker algorithm and the automatically calculated tolerance if this doesn't exceed the specified tolerance\. This algorithm calculates the size to store objects within the tolerance specified\. The *tolerance* value is optional\.
For examples of loading shapefiles, see [Loading a shapefile into Amazon Redshift](r_COPY_command_examples.md#copy-example-spatial-copy-shapefile)\.

AVRO \[AS\] '*avro\_option*'  <a name="copy-avro"></a>
Specifies that the source data is in Avro format\.   
Avro format is supported for COPY from these services and protocols:  
+ Amazon S3 
+ Amazon EMR 
+ Remote hosts \(SSH\) 
Avro isn't supported for COPY from DynamoDB\.   
Avro is a data serialization protocol\. An Avro source file includes a schema that defines the structure of the data\. The Avro schema type must be `record`\. COPY accepts Avro files creating using the default uncompressed codec as well as the `deflate` and `snappy` compression codecs\. For more information about Avro, go to [Apache Avro](https://avro.apache.org/)\.   
Valid values for *avro\_option* are as follows:  
+ `'auto'`
+ `'auto ignorecase'`
+ `'s3://jsonpaths_file'` 
The default is `'auto'`\.  
COPY automatically maps the data elements in the Avro source data to the columns in the target table\. It does so by matching field names in the Avro schema to column names in the target table\. The matching is case\-sensitive for `'auto'` and isn't case\-sensitive for `'auto ignorecase'`\.   
Column names in Amazon Redshift tables are always lowercase, so when you use the `'auto'` option, matching field names must also be lowercase\. If the field names aren't all lowercase, you can use the `'auto ignorecase'` option\. With the default `'auto'` argument, COPY recognizes only the first level of fields, or *outer fields*, in the structure\.   
To explicitly map column names to Avro field names, you can use a [JSONPaths file](#copy-json-jsonpaths)\.   
By default, COPY attempts to match all columns in the target table to Avro field names\. To load a subset of the columns, you can optionally specify a column list\. If a column in the target table is omitted from the column list, COPY loads the target column's [DEFAULT](r_CREATE_TABLE_NEW.md#create-table-default) expression\. If the target column doesn't have a default, COPY attempts to load NULL\. If a column is included in the column list and COPY doesn't find a matching field in the Avro data, COPY attempts to load NULL to the column\.   
If COPY attempts to assign NULL to a column that is defined as NOT NULL, the COPY command fails\.   
<a name="copy-avro-schema"></a>**Avro Schema**  
An Avro source data file includes a schema that defines the structure of the data\. COPY reads the schema that is part of the Avro source data file to map data elements to target table columns\. The following example shows an Avro schema\.   

```
{
    "name": "person",
    "type": "record",
    "fields": [
        {"name": "id", "type": "int"},
        {"name": "guid", "type": "string"},
        {"name": "name", "type": "string"},
        {"name": "address", "type": "string"}]
}
```
The Avro schema is defined using JSON format\. The top\-level JSON object contains three name\-value pairs with the names, or *keys*, `"name"`, `"type"`, and `"fields"`\.   
The `"fields"` key pairs with an array of objects that define the name and data type of each field in the data structure\. By default, COPY automatically matches the field names to column names\. Column names are always lowercase, so matching field names must also be lowercase, unless you specify the `‘auto ignorecase’` option\. Any field names that don't match a column name are ignored\. Order doesn't matter\. In the previous example, COPY maps to the column names `id`, `guid`, `name`, and `address`\.   
With the default `'auto'` argument, COPY matches only the first\-level objects to columns\. To map to deeper levels in the schema, or if field names and column names don't match, use a JSONPaths file to define the mapping\. For more information, see [JSONPaths file](#copy-json-jsonpaths)\.   
If the value associated with a key is a complex Avro data type such as byte, array, record, map, or link, COPY loads the value as a string\. Here, the string is the JSON representation of the data\. COPY loads Avro enum data types as strings, where the content is the name of the type\. For an example, see [COPY from JSON format](copy-usage_notes-copy-from-json.md)\.  
The maximum size of the Avro file header, which includes the schema and file metadata, is 1 MB\.     
The maximum size of a single Avro data block is 4 MB\. This is distinct from the maximum row size\. If the maximum size of a single Avro data block is exceeded, even if the resulting row size is less than the 4 MB row\-size limit, the COPY command fails\.   
In calculating row size, Amazon Redshift internally counts pipe characters \( \| \) twice\. If your input data contains a very large number of pipe characters, it is possible for row size to exceed 4 MB even if the data block is less than 4 MB\.

JSON \[AS\] '*json\_option*'  <a name="copy-json"></a>
The source data is in JSON format\.   
JSON format is supported for COPY from these services and protocols:  
+ Amazon S3
+ COPY from Amazon EMR
+ COPY from SSH
JSON isn't supported for COPY from DynamoDB\.   
Valid values for *json\_option* are as follows :  
+ `'auto'`
+ `'auto ignorecase'`
+ `'s3://jsonpaths_file'` 
The default is `'auto'`\.  
By default, COPY attempts to match all columns in the target table to JSON field name keys\. To load a subset of the columns, you can optionally specify a column list\. If the JSON field name keys aren't all lowercase, you can use the `'auto ignorecase'` option or a [JSONPaths file](#copy-json-jsonpaths) to explicitly map column names to JSON field name keys\.  
If a column in the target table is omitted from the column list, then COPY loads the target column's [DEFAULT](r_CREATE_TABLE_NEW.md#create-table-default) expression\. If the target column doesn't have a default, COPY attempts to load NULL\. If a column is included in the column list and COPY doesn't find a matching field in the JSON data, COPY attempts to load NULL to the column\.   
If COPY attempts to assign NULL to a column that is defined as NOT NULL, the COPY command fails\.   
COPY maps the data elements in the JSON source data to the columns in the target table\. It does so by matching *object keys*, or names, in the source name\-value pairs to the names of columns in the target table\.   
Refer to the following details about each *json\_option* value:    
'auto'  <a name="copy-json-auto"></a>
With this option, matching is case\-sensitive\. Column names in Amazon Redshift tables are always lowercase, so when you use the `'auto'` option, matching JSON field names must also be lowercase\.  
'auto ignorecase'  <a name="copy-json-auto-ignorecase"></a>
With this option, the matching isn't case\-sensitive\. Column names in Amazon Redshift tables are always lowercase, so when you use the `'auto ignorecase'` option, the corresponding JSON field names can be lowercase, uppercase, or mixed case\.   
's3://*jsonpaths\_file*'  <a name="copy-json-pathfile"></a>
With this option, COPY uses the named JSONPaths file to map the data elements in the JSON source data to the columns in the target table\. The *`s3://jsonpaths_file`* argument must be an Amazon S3 object key that explicitly references a single file\. An example is `'s3://mybucket/jsonpaths.txt`'\. The argument can't be a key prefix\. For more information about using a JSONPaths file, see [JSONPaths file](#copy-json-jsonpaths)\.  
In some cases, the file specified by `jsonpaths_file` has the same prefix as the path specified by `copy_from_s3_objectpath` for the data files\. If so, COPY reads the JSONPaths file as a data file and returns errors\. For example, suppose that your data files use the object path `s3://mybucket/my_data.json` and your JSONPaths file is `s3://mybucket/my_data.jsonpaths`\. In this case, COPY attempts to load `my_data.jsonpaths` as a data file\.

## JSON data file<a name="copy-json-data-file"></a>

The JSON data file contains a set of either objects or arrays\. COPY loads each JSON object or array into one row in the target table\. Each object or array corresponding to a row must be a stand\-alone, root\-level structure; that is, it must not be a member of another JSON structure\.

A JSON *object* begins and ends with braces  \( \{ \} \) and contains an unordered collection of name\-value pairs\. Each paired name and value are separated by a colon, and the pairs are separated by commas\. By default, the *object key*, or name, in the name\-value pairs must match the name of the corresponding column in the table\. Column names in Amazon Redshift tables are always lowercase, so matching JSON field name keys must also be lowercase\. If your column names and JSON keys don't match, use a [JSONPaths file](#copy-json-jsonpaths) to explicitly map columns to keys\. 

Order in a JSON object doesn't matter\. Any names that don't match a column name are ignored\. The following shows the structure of a simple JSON object\.

```
{
  "column1": "value1",
  "column2": value2,
  "notacolumn" : "ignore this value"
}
```

A JSON *array* begins and ends with brackets \( \[  \] \), and contains an ordered collection of values separated by commas\. If your data files use arrays, you must specify a JSONPaths file to match the values to columns\. The following shows the structure of a simple JSON array\. 

```
["value1", value2]
```

The JSON must be well\-formed\. For example, the objects or arrays can't be separated by commas or any other characters except white space\. Strings must be enclosed in double quotation mark characters\. Quote characters must be simple quotation marks \(0x22\), not slanted or "smart" quotation marks\.

The maximum size of a single JSON object or array, including braces or brackets, is 4 MB\. This is distinct from the maximum row size\. If the maximum size of a single JSON object or array is exceeded, even if the resulting row size is less than the 4 MB row\-size limit, the COPY command fails\. 

In calculating row size, Amazon Redshift internally counts pipe characters \( \| \) twice\. If your input data contains a very large number of pipe characters, it is possible for row size to exceed 4 MB even if the object size is less than 4 MB\.

COPY loads `\n` as a newline character and loads `\t` as a tab character\. To load a backslash, escape it with a backslash \( `\\` \)\.

COPY searches the specified JSON source for a well\-formed, valid JSON object or array\. If COPY encounters any non–white\-space characters before locating a usable JSON structure, or between valid JSON objects or arrays, COPY returns an error for each instance\. These errors count toward the MAXERROR error count\. When the error count equals or exceeds MAXERROR, COPY fails\. 

For each error, Amazon Redshift records a row in the STL\_LOAD\_ERRORS system table\. The LINE\_NUMBER column records the last line of the JSON object that caused the error\. 

If IGNOREHEADER is specified, COPY ignores the specified number of lines in the JSON data\. Newline characters in the JSON data are always counted for IGNOREHEADER calculations\. 

COPY loads empty strings as empty fields by default\. If EMPTYASNULL is specified, COPY loads empty strings for CHAR and VARCHAR fields as NULL\. Empty strings for other data types, such as INT, are always loaded with NULL\. 

The following options aren't supported with JSON: 
+ CSV
+ DELIMITER 
+ ESCAPE
+ FILLRECORD 
+ FIXEDWIDTH
+ IGNOREBLANKLINES
+ NULL AS
+ READRATIO
+ REMOVEQUOTES 

For more information, see [COPY from JSON format](copy-usage_notes-copy-from-json.md)\. For more information about JSON data structures, go to [www\.json\.org](https://www.json.org/)\. 

## JSONPaths file<a name="copy-json-jsonpaths"></a>

If you are loading from JSON\-formatted or Avro source data, by default COPY maps the first\-level data elements in the source data to the columns in the target table\. It does so by matching each name, or object key, in a name\-value pair to the name of a column in the target table\. 

If your column names and object keys don't match, or to map to deeper levels in the data hierarchy, you can use a JSONPaths file to explicitly map JSON or Avro data elements to columns\. The JSONPaths file maps JSON data elements to columns by matching the column order in the target table or column list\.

The JSONPaths file must contain only a single JSON object \(not an array\)\. The JSON object is a name\-value pair\. The *object key*, which is the name in the name\-value pair, must be `"jsonpaths"`\. The *value* in the name\-value pair is an array of *JSONPath expressions*\. Each JSONPath expression references a single element in the JSON data hierarchy or Avro schema, similarly to how an XPath expression refers to elements in an XML document\. For more information, see [JSONPath expressions](#copy-json-jsonpath-expressions)\.

To use a JSONPaths file, add the JSON or AVRO keyword to the COPY command\. Specify the S3 bucket name and object path of the JSONPaths file using the following format\.

```
COPY tablename 
FROM 'data_source' 
CREDENTIALS 'credentials-args' 
FORMAT AS { AVRO | JSON } 's3://jsonpaths_file';
```

The `s3://jsonpaths_file` value must be an Amazon S3 object key that explicitly references a single file, such as `'s3://mybucket/jsonpaths.txt'`\. It can't be a key prefix\. 

In some cases, if you're loading from Amazon S3 the file specified by `jsonpaths_file` has the same prefix as the path specified by `copy_from_s3_objectpath` for the data files\. If so, COPY reads the JSONPaths file as a data file and returns errors\. For example, suppose that your data files use the object path `s3://mybucket/my_data.json` and your JSONPaths file is `s3://mybucket/my_data.jsonpaths`\. In this case, COPY attempts to load `my_data.jsonpaths` as a data file\.

 If the key name is any string other than `"jsonpaths"`, the COPY command doesn't return an error, but it ignores *jsonpaths\_file* and uses the `'auto'` argument instead\. 

If any of the following occurs, the COPY command fails:
+ The JSON is malformed\.
+ There is more than one JSON object\.
+ Any characters except white space exist outside the object\.
+ An array element is an empty string or isn't a string\.

MAXERROR doesn't apply to the JSONPaths file\. 

The JSONPaths file must not be encrypted, even if the [ENCRYPTED](copy-parameters-data-source-s3.md#copy-encrypted) option is specified\.

For more information, see [COPY from JSON format](copy-usage_notes-copy-from-json.md)\. 

## JSONPath expressions<a name="copy-json-jsonpath-expressions"></a>

The JSONPaths file uses JSONPath expressions to map data fields to target columns\. Each JSONPath expression corresponds to one column in the Amazon Redshift target table\. The order of the JSONPath array elements must match the order of the columns in the target table or the column list, if a column list is used\. 

The double quotation mark characters are required as shown, both for the field names and the values\. The quotation mark characters must be simple quotation marks \(0x22\), not slanted or "smart" quotation marks\.

If an object element referenced by a JSONPath expression isn't found in the JSON data, COPY attempts to load a NULL value\. If the referenced object is malformed, COPY returns a load error\. 

If an array element referenced by a JSONPath expression isn't found in the JSON or Avro data, COPY fails with the following error: `Invalid JSONPath format: Not an array or index out of range.` Remove any array elements from the JSONPaths that don't exist in the source data and verify that the arrays in the source data are well formed\.  

The JSONPath expressions can use either bracket notation or dot notation, but you can't mix notations\. The following example shows JSONPath expressions using bracket notation\. 

```
{
    "jsonpaths": [
        "$['venuename']",
        "$['venuecity']",
        "$['venuestate']",
        "$['venueseats']"
    ]
}
```

The following example shows JSONPath expressions using dot notation\. 

```
{
    "jsonpaths": [
        "$.venuename",
        "$.venuecity",
        "$.venuestate",
        "$.venueseats"
    ]
}
```

In the context of Amazon Redshift COPY syntax, a JSONPath expression must specify the explicit path to a single name element in a JSON or Avro hierarchical data structure\. Amazon Redshift doesn't support any JSONPath elements, such as wildcard characters or filter expressions, that might resolve to an ambiguous path or multiple name elements\.

For more information, see [COPY from JSON format](copy-usage_notes-copy-from-json.md)\. 

## Using JSONPaths with Avro Data<a name="using-jsonpath-with-avro"></a>

The following example shows an Avro schema with multiple levels\.

```
{
    "name": "person",
    "type": "record",
    "fields": [
        {"name": "id", "type": "int"},
        {"name": "guid", "type": "string"},
        {"name": "isActive", "type": "boolean"},
        {"name": "age", "type": "int"},
        {"name": "name", "type": "string"},
        {"name": "address", "type": "string"},
        {"name": "latitude", "type": "double"},
        {"name": "longitude", "type": "double"},
        {
            "name": "tags",
            "type": {
                        "type" : "array",
                        "name" : "inner_tags",
                        "items" : "string"
                    }
        },
        {
            "name": "friends",
            "type": {
                        "type" : "array",
                        "name" : "inner_friends",
                        "items" : {
                                    "name" : "friends_record",
                                    "type" : "record",
                                    "fields" : [
                                                 {"name" : "id", "type" : "int"},
                                                 {"name" : "name", "type" : "string"}
                                               ]
                                  }
                    }
        },
        {"name": "randomArrayItem", "type": "string"}
    ]
}
```

The following example shows a JSONPaths file that uses AvroPath expressions to reference the previous schema\. 

```
{
    "jsonpaths": [
        "$.id",
        "$.guid",
        "$.address",
        "$.friends[0].id"
    ]
}
```

The JSONPaths example includes the following elements:

jsonpaths  
The name of the JSON object that contains the AvroPath expressions\.

\[ … \]  
Brackets enclose the JSON array that contains the path elements\.

$  
The dollar sign refers to the root element in the Avro schema, which is the `"fields"` array\.

"$\.id",  
The target of the AvroPath expression\. In this instance, the target is the element in the `"fields"` array with the name `"id"`\. The expressions are separated by commas\.

"$\.friends\[0\]\.id"  
Brackets indicate an array index\. JSONPath expressions use zero\-based indexing, so this expression references the first element in the `"friends"` array with the name `"id"`\.

The Avro schema syntax requires using *inner fields* to define the structure of record and array data types\. The inner fields are ignored by the AvroPath expressions\. For example, the field `"friends"` defines an array named `"inner_friends"`, which in turn defines a record named `"friends_record"`\. The AvroPath expression to reference the field `"id"` can ignore the extra fields to reference the target field directly\. The following AvroPath expressions reference the two fields that belong to the `"friends"` array\.

```
"$.friends[0].id"
"$.friends[0].name"
```

## Columnar data format parameters<a name="copy-parameters-columnar-data"></a>

In addition to the standard data formats, COPY supports the following columnar data formats for COPY from Amazon S3\. COPY from columnar format is supported with certain restrictions\. For more information, see [COPY from columnar data formats](copy-usage_notes-copy-from-columnar.md)\. 

ORC  <a name="copy-orc"></a>
Loads the data from a file that uses Optimized Row Columnar \(ORC\) file format\. 

PARQUET  <a name="copy-parquet"></a>
Loads the data from a file that uses Parquet file format\. 