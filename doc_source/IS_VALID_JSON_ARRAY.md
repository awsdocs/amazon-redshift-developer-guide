# IS\_VALID\_JSON\_ARRAY function<a name="IS_VALID_JSON_ARRAY"></a>

IS\_VALID\_JSON\_ARRAY validates a JSON array\. The function returns Boolean `true` \(`t`\) if the array is properly formed JSON or `false` \(`f`\) if the array is malformed\. To validate a JSON string, use [IS\_VALID\_JSON function](IS_VALID_JSON.md)

For more information, see [JSON functions](json-functions.md)\. 

## Syntax<a name="IS_VALID_JSON_ARRAY-synopsis"></a>

```
is_valid_json_array('json_array') 
```

## Arguments<a name="IS_VALID_JSON_ARRAY-arguments"></a>

 *json\_array*  
A string or expression that evaluates to a JSON array\.

## Return type<a name="IS_VALID_JSON_ARRAY-return"></a>

BOOLEAN

## Example<a name="IS_VALID_JSON_ARRAY-examples"></a>

The following example creates a table and inserts JSON strings for testing\.

```
create table test_json_arrays(id int identity(0,1), json_arrays varchar);

-- Insert valid JSON array strings --
insert into test_json_arrays(json_arrays) 
values('[]'), 
('["a","b"]'), 
('["a",["b",1,["c",2,3,null]]]');

-- Insert invalid JSON array strings --
insert into test_json_arrays(json_arrays) values
('{"a":1}'),
('a'),
('[1,2,]');
```

The following example validates the strings in the preceding example\.

```
select json_arrays, is_valid_json_array(json_arrays) 
from test_json_arrays order by id;

json_arrays                  | is_valid_json_array
-----------------------------+--------------------
[]                           | true               
["a","b"]                    | true               
["a",["b",1,["c",2,3,null]]] | true               
{"a":1}                      | false              
a                            | false              
[1,2,]                       | false
```