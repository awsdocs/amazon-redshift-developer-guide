# array\_flatten function<a name="array_flatten"></a>

Merges multiple arrays into a single array of SUPER type\.

## Syntax<a name="array_flatten-syntax"></a>

```
array_flatten(super_expr1,super_expr2,..)
```

## Arguments<a name="array_flatten-arguments"></a>

 *super\_expr1*,*super\_expr2*   
A valid super expression of array form\. 

## Return type<a name="array_flatten-returm-type"></a>

The array\_flatten function returns a SUPER data value\.

## Example<a name="array_flatten-example"></a>

The following example shows an array\_flatten function\. 

```
SELECT ARRAY_FLATTEN(ARRAY(ARRAY(1,2,3,4),ARRAY(5,6,7,8),ARRAY(9,10)));
     array_flatten
------------------------
 [1,2,3,4,5,6,7,8,9,10]
(1 row)
```