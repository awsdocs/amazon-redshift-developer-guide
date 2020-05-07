# IS\_VALID\_JSON function<a name="IS_VALID_JSON"></a>

IS\_VALID\_JSON validates a JSON string\. The function returns Boolean `true` \(`t`\) if the string is properly formed JSON or `false` \(`f`\) if the string is malformed\. To validate a JSON array, use [IS\_VALID\_JSON\_ARRAY function](IS_VALID_JSON_ARRAY.md)

For more information, see [JSON functions](json-functions.md)\. 

## Syntax<a name="IS_VALID_JSON-synopsis"></a>

```
is_valid_json('json_string')
```

## Arguments<a name="IS_VALID_JSON-arguments"></a>

 *json\_string*  
A string or expression that evaluates to a JSON string\.

## Return type<a name="IS_VALID_JSON-return"></a>

BOOLEAN

## Example<a name="IS_VALID_JSON-examples"></a>

The following example creates a table and inserts JSON strings for testing\.

```
create table test_json(id int identity(0,1), json_strings varchar);

-- Insert valid JSON strings --
insert into test_json(json_strings) values
('{"a":2}'), 
('{"a":{"b":{"c":1}}}'), 
('{"a": [1,2,"b"]}');

-- Insert invalid JSON strings --
insert into test_json(json_strings)values
('{{}}'), 
('{1:"a"}'), 
('[1,2,3]');
```

The following example validates the strings in the preceding example\.

```
select id, json_strings, is_valid_json(json_strings) 
from test_json order by id;

id | json_strings        | is_valid_json
---+---------------------+--------------
 0 | {"a":2}             | true         
 2 | {"a":{"b":{"c":1}}} | true         
 4 | {"a": [1,2,"b"]}    | true         
 6 | {{}}                | false        
 8 | {1:"a"}             | false        
10 | [1,2,3]             | false
```