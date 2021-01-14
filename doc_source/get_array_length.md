# get\_array\_length function<a name="get_array_length"></a>

Returns the length of the specified array\. The GET\_ARRAY\_LENGTH function returns the length of a SUPER array given an object or array path\.

## Syntax<a name="get_array_length-syntax"></a>

```
get_array_length(super_expr)
```

## Arguments<a name="get_array_length-arguments"></a>

 *super\_expr*   
A valid super expression of array form\.

## Return type<a name="get_array_length-returm-type"></a>

The get\_array\_length function returns a BIGINT\. 

## Example<a name="get_array_length-example"></a>

The following example shows a get\_array\_length function\.

```
SELECT GET_ARRAY_LENGTH(ARRAY(1,2,3,4,5,6,7,8,9,10));
 get_array_length
----------------------
            10
(1 row)
```