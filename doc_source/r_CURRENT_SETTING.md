# CURRENT\_SETTING<a name="r_CURRENT_SETTING"></a>

 CURRENT\_SETTING returns the current value of the specified configuration parameter\.

This function is equivalent to the [SHOW](r_SHOW.md) command\.

## Syntax<a name="r_CURRENT_SETTING-synopsis"></a>

```
current_setting('parameter')
```

## Argument<a name="r_CURRENT_SETTING-argument"></a>

 *parameter*   
Parameter value to display\. For a list of configuration parameters, see [Configuration reference](cm_chap_ConfigurationRef.md)

## Return type<a name="r_CURRENT_SETTING-return-type"></a>

Returns a CHAR or VARCHAR string\.

## Example<a name="r_CURRENT_SETTING-example"></a>

The following query returns the current setting for the `query_group` parameter:

```
select current_setting('query_group');

current_setting
-----------------
unset
(1 row)
```