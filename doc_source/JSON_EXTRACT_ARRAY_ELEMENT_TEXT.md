# JSON\_EXTRACT\_ARRAY\_ELEMENT\_TEXT function<a name="JSON_EXTRACT_ARRAY_ELEMENT_TEXT"></a>

JSON\_EXTRACT\_ARRAY\_ELEMENT\_TEXT returns a JSON array element in the outermost array of a JSON string, using a zero\-based index\. The first element in an array is at position 0\. If the index is negative or out of bound, JSON\_EXTRACT\_ARRAY\_ELEMENT\_TEXT returns empty string\. If the *null\_if\_invalid* argument is set to `true` and the JSON string is invalid, the function returns NULL instead of returning an error\.

For more information, see [JSON functions](json-functions.md)\. 

## Syntax<a name="JSON_EXTRACT_ARRAY_ELEMENT_TEXT-synopsis"></a>

```
json_extract_array_element_text('json string', pos [, null_if_invalid ] )
```

## Arguments<a name="JSON_EXTRACT_ARRAY_ELEMENT_TEXT-arguments"></a>

 *json\_string*  
A properly formatted JSON string\.

*pos*  
An integer representing the index of the array element to be returned, using a zero\-based array index\.

*null\_if\_invalid*  
A Boolean value that specifies whether to return NULL if the input JSON string is invalid instead of returning an error\. To return NULL if the JSON is invalid, specify `true` \(`t`\)\. To return an error if the JSON is invalid, specify `false` \(`f`\)\. The default is `false`\.

## Return type<a name="JSON_EXTRACT_ARRAY_ELEMENT_TEXT-return"></a>

A VARCHAR string representing the JSON array element referenced by *pos*\.

## Example<a name="JSON_EXTRACT_ARRAY_ELEMENT_TEXT-examples"></a>

The following example returns array element at position 2, which is the third element of a zero\-based array index: 

```
select json_extract_array_element_text('[111,112,113]', 2);
 
json_extract_array_element_text 
-------------------------------
113
```

The following example returns an error because the JSON is invalid\.

```
select json_extract_array_element_text('["a",["b",1,["c",2,3,null,]]]',1);
 
An error occurred when executing the SQL command:
select json_extract_array_element_text('["a",["b",1,["c",2,3,null,]]]',1)
```

The following example sets *null\_if\_invalid* to *true*, so the statement returns NULL instead of returning an error for invalid JSON\.

```
select json_extract_array_element_text('["a",["b",1,["c",2,3,null,]]]',1,true);
 
json_extract_array_element_text
-------------------------------
```