# JSON\_EXTRACT\_PATH\_TEXT function<a name="JSON_EXTRACT_PATH_TEXT"></a>

JSON\_EXTRACT\_PATH\_TEXT returns the value for the *key:value* pair referenced by a series of path elements in a JSON string\. The JSON path can be nested up to five levels deep\. Path elements are case\-sensitive\. If a path element does not exist in the JSON string, JSON\_EXTRACT\_PATH\_TEXT returns an empty string\. If the `null_if_invalid` argument is set to `true` and the JSON string is invalid, the function returns NULL instead of returning an error\.

For more information, see [JSON functions](json-functions.md)\. 

## Syntax<a name="JSON_EXTRACT_PATH_TEXT-synopsis"></a>

```
json_extract_path_text('json_string', 'path_elem' [,'path_elem'[, …] ] [, null_if_invalid ] )
```

## Arguments<a name="JSON_EXTRACT_PATH_TEXT-arguments"></a>

 *json\_string*  
A properly formatted JSON string\.

*path\_elem*  
A path element in a JSON string\. One path element is required\. Additional path elements can be specified, up to five levels deep\.

*null\_if\_invalid*  
A Boolean value that specifies whether to return NULL if the input JSON string is invalid instead of returning an error\. To return NULL if the JSON is invalid, specify `true` \(`t`\)\. To return an error if the JSON is invalid, specify `false` \(`f`\)\. The default is `false`\.

In a JSON string, Amazon Redshift recognizes `\n` as a newline character and `\t` as a tab character\. To load a backslash, escape it with a backslash \(`\\`\)\. For more information, see [Escape characters in JSON](copy-usage_notes-copy-from-json.md#copy-usage-json-escape-characters)\.

## Return type<a name="JSON_EXTRACT_PATH_TEXT-return"></a>

VARCHAR string representing the JSON value referenced by the path elements\.

## Example<a name="JSON_EXTRACT_PATH_TEXT-examples"></a>

The following example returns the value for the path `'f4', 'f6'`: 

```
select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"star"}}','f4', 'f6');

json_extract_path_text
---------------------- 
star
```

The following example returns an error because the JSON is invalid\.

```
select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"star"}','f4', 'f6');
 
An error occurred when executing the SQL command:
select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"star"}','f4', 'f6')
```

The following example sets *null\_if\_invalid* to *true*, so the statement returns NULL for invalid JSON instead of returning an error\.

```
select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"star"}','f4', 'f6',true);
 
json_extract_path_text
-------------------------------
```