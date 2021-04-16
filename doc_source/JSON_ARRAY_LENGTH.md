# JSON\_ARRAY\_LENGTH function<a name="JSON_ARRAY_LENGTH"></a>

JSON\_ARRAY\_LENGTH returns the number of elements in the outer array of a JSON string\. If the *null\_if\_invalid* argument is set to `true` and the JSON string is invalid, the function returns NULL instead of returning an error\.

For more information, see [JSON functions](json-functions.md)\. 

## Syntax<a name="JSON_ARRAY_LENGTH-synopsis"></a>

```
json_array_length('json_array' [, null_if_invalid ] ) 
```

## Arguments<a name="JSON_ARRAY_LENGTH-arguments"></a>

 *json\_array*  
A properly formatted JSON array\.

 *null\_if\_invalid*  
A Boolean value that specifies whether to return NULL if the input JSON string is invalid instead of returning an error\. To return NULL if the JSON is invalid, specify `true` \(`t`\)\. To return an error if the JSON is invalid, specify `false` \(`f`\)\. The default is `false`\.

## Return type<a name="JSON_ARRAY_LENGTH-return"></a>

INTEGER

## Example<a name="JSON_ARRAY_LENGTH-examples"></a>

The following example returns the number of elements in the array: 

```
select json_array_length('[11,12,13,{"f1":21,"f2":[25,26]},14]'); 

json_array_length 
-----------------
5
```

The following example returns an error because the JSON is invalid\.

```
select json_array_length('[11,12,13,{"f1":21,"f2":[25,26]},14');
 
An error occurred when executing the SQL command:
select json_array_length('[11,12,13,{"f1":21,"f2":[25,26]},14')
```

The following example sets *null\_if\_invalid* to *true*, so the statement the returns NULL instead of returning an error for invalid JSON\.

```
select json_array_length('[11,12,13,{"f1":21,"f2":[25,26]},14',true);

json_array_length
-----------------
```