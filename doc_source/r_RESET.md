# RESET<a name="r_RESET"></a>

Restores the value of a configuration parameter to its default value\.

You can reset either a single specified parameter or all parameters at once\. To set a parameter to a specific value, use the [SET](r_SET.md) command\. To display the current value of a parameter, use the [SHOW](r_SHOW.md) command\.

## Syntax<a name="r_RESET-synopsis"></a>

```
RESET { parameter_name | ALL }
```

## Parameters<a name="r_RESET-parameters"></a>

 *parameter\_name*   
Name of the parameter to reset\. See [Modifying the Server Configuration](t_Modifying_the_default_settings.md) for more documentation about parameters\.

ALL   
Resets all run\-time parameters\.

## Examples<a name="r_RESET-examples"></a>

The following example resets the `query_group` parameter to its default value: 

```
reset query_group;
```

The following example resets all run\-time parameters to their default values: 

```
reset all;
```