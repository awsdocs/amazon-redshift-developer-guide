# JSON functions<a name="json-functions"></a>

**Topics**
+ [IS\_VALID\_JSON function](IS_VALID_JSON.md)
+ [IS\_VALID\_JSON\_ARRAY function](IS_VALID_JSON_ARRAY.md)
+ [JSON\_ARRAY\_LENGTH function](JSON_ARRAY_LENGTH.md)
+ [JSON\_EXTRACT\_ARRAY\_ELEMENT\_TEXT function](JSON_EXTRACT_ARRAY_ELEMENT_TEXT.md)
+ [JSON\_EXTRACT\_PATH\_TEXT function](JSON_EXTRACT_PATH_TEXT.md)

When you need to store a relatively small set of key\-value pairs, you might save space by storing the data in JSON format\. Because JSON strings can be stored in a single column, using JSON might be more efficient than storing your data in tabular format\. For example, suppose you have a sparse table, where you need to have many columns to fully represent all possible attributes, but most of the column values are NULL for any given row or any given column\. By using JSON for storage, you might be able to store the data for a row in key:value pairs in a single JSON string and eliminate the sparsely\-populated table columns\. 

In addition, you can easily modify JSON strings to store additional key:value pairs without needing to add columns to a table\. 

We recommend using JSON sparingly\. JSON is not a good choice for storing larger datasets because, by storing disparate data in a single column, JSON does not use the Amazon Redshift column store architecture\. 

JSON uses UTF\-8 encoded text strings, so JSON strings can be stored as CHAR or VARCHAR data types\. Use VARCHAR if the strings include multi\-byte characters\.

JSON strings must be properly formatted JSON, according to the following rules: 
+ The root level JSON can either be a JSON object or a JSON array\. A JSON object is an unordered set of comma\-separated key:value pairs enclosed by curly braces\. 

  For example, `{"one":1, "two":2} `
+ A JSON array is an ordered set of comma\-separated values enclosed by brackets\. 

  An example is the following: `["first", {"one":1}, "second", 3, null] `
+ JSON arrays use a zero\-based index; the first element in an array is at position 0\. In a JSON key:value pair, the key is a string in double quotation marks\. 
+ A JSON value can be any of the following: 
  + JSON object 
  + JSON array 
  + string in double quotation marks
  + number \(integer and float\) 
  + boolean
  + null 
+ Empty objects and empty arrays are valid JSON values\.
+ JSON fields are case sensitive\. 
+ White space between JSON structural elements \(such as `{ }, [ ]`\) is ignored\. 

The Amazon Redshift JSON functions and the Amazon Redshift COPY command use the same methods to work with JSON\-formatted data\. For more information about working with JSON, see [COPY from JSON format](copy-usage_notes-copy-from-json.md)