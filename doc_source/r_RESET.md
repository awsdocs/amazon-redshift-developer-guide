# RESET<a name="r_RESET"></a>

Restores the value of a configuration parameter to its default value\.

You can reset either a single specified parameter or all parameters at once\. To set a parameter to a specific value, use the [SET](r_SET.md) command\. To display the current value of a parameter, use the [SHOW](r_SHOW.md) command\.

## Syntax<a name="r_RESET-synopsis"></a>

```
RESET { parameter_name | ALL }
```

The following statement sets the value of a session context variable to NULL\.

```
RESET { variable_name | ALL }
```

## Parameters<a name="r_RESET-parameters"></a>

 *parameter\_name*   
Name of the parameter to reset\. See [Modifying the server configuration](cm_chap_ConfigurationRef.md#t_Modifying_the_default_settings) for more documentation about parameters\.

ALL   
Resets all runtime parameters, including all the session context variables\.

*variable*   
The name of the variable to reset\. If the value to RESET is a session context variable, Amazon Redshift sets it to NULL\.

## Examples<a name="r_RESET-examples"></a>

The following example resets the `query_group` parameter to its default value: 

```
reset query_group;
```

The following example resets all runtime parameters to their default values\. 

```
reset all;
```

The following example resets the context variable\.

```
RESET app_context.user_id;
```