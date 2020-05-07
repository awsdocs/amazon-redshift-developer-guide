# SET\_CONFIG<a name="r_SET_CONFIG"></a>

Sets a configuration parameter to a new setting\.

 This function is equivalent to the SET command in SQL\.

## Syntax<a name="r_SET_CONFIG-synopsis"></a>

```
set_config('parameter', 'new_value' , is_local)
```

## Arguments<a name="r_SET_CONFIG-parameters"></a>

 *parameter*   
Parameter to set\.

 *new\_value*   
New value of the parameter\.

 *is\_local*   
If true, parameter value applies only to the current transaction\. Valid values are `true` or `1` and `false` or `0`\. 

## Return type<a name="r_SET_CONFIG-return-type"></a>

Returns a CHAR or VARCHAR string\.

## Examples<a name="r_SET_CONFIG-examples"></a>

The following query sets the value of the `query_group` parameter to `test` for the current transaction only: 

```
select set_config('query_group', 'test', true);

set_config
------------
test
(1 row)
```