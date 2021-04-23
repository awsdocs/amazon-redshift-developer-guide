# subarray function<a name="r_subarray"></a>

Manipulates arrays to return a subset of the input arrays\.

### Syntax<a name="r_subarray-syntax"></a>

```
SUBARRAY( super_expr, start_position, length )
```

### Arguments<a name="r_subarray-arguments"></a>

*super\_expr*  
A valid super expression in array form\.

*start\_position*  
The position within the array to begin the extraction, starting at index position 0\. A negative position counts backward from the end of the array\.

*length*  
The number of elements to extract \(the length of the substring\)\.

### Return type<a name="r_subarray-return-type"></a>

The subarray function returns a SUPER data value\.

### Examples<a name="r_subarray-examples"></a>

The following is an example of a subarray function\.

```
 SELECT SUBARRAY(ARRAY('a', 'b', 'c', 'd', 'e', 'f'), 2, 3);
   subarray
---------------
 ["c","d","e"]
(1 row)
```