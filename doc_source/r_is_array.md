# IS\_ARRAY function<a name="r_is_array"></a>

Checks whether a variable is an array\. The function returns true if the variable is an array\. The function also includes empty arrays\. Otherwise, the function returns false for all other values, including null\.

## Syntax<a name="r_is_array-synopsis"></a>

```
IS_ARRAY (super_expression)
```

## Arguments<a name="r_is_array-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_array-returns"></a>

Boolean

## Example<a name="r_is_array_example"></a>

The following query shows an IS\_ARRAY function\.

```
SELECT is_array(JSON_PARSE('[1,2]'));
 is_array
----------
   True
(1 row)
```