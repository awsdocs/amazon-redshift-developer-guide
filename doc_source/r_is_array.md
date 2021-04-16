# is\_array function<a name="r_is_array"></a>

Checks whether there is a variable in an array\. The function returns true if the variable is an array\. The function also includes empty arrays\. Otherwise, the function returns false for all other values, including null\.

## Example<a name="r_is_array_example"></a>

The following query shows an is\_array function\.

```
SELECT is_array(JSON_PARSE('[1,2]'));
 is_array
----------
   True
(1 row)
```