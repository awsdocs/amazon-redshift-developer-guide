# SHOW<a name="r_SHOW"></a>

Displays the current value of a server configuration parameter\. This value may be specific to the current session if a SET command is in effect\. For a list of configuration parameters, see [Configuration reference](cm_chap_ConfigurationRef.md)\.

## Syntax<a name="r_SHOW-synopsis"></a>

```
SHOW { parameter_name | ALL }
```

## Parameters<a name="r_SHOW-parameters"></a>

 *parameter\_name*   
Displays the current value of the specified parameter\.

ALL   
Displays the current values of all of the parameters\.

## Examples<a name="r_SHOW-examples"></a>

The following example displays the value for the query\_group parameter: 

```
show query_group;

query_group

unset
(1 row)
```

The following example displays a list of all parameters and their values: 

```
show all;
name        |   setting
--------------------+--------------
datestyle          | ISO, MDY
extra_float_digits | 0
query_group        | unset
search_path        | $user,public
statement_timeout  | 0
```