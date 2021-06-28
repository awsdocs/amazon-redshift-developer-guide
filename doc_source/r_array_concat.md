# array\_concat function<a name="r_array_concat"></a>

The array\_conact function concatenates two arrays to create an array that contains all the elements in the first array followed by all the elements in the second array\. The two arguments must be valid arrays\.

## Syntax<a name="r_array_concat-synopsis"></a>

```
array_concat( super_expr1,  super_expr2 )
```

## Arguments<a name="r_array_concat-argument-arguments"></a>

 *super\_expr1*  
The value that specifies the first of the two arrays to concatenate\.

 *super\_expr2*  
The value that specifies the second of the two arrays to concatenate\.

## Return type<a name="r_array_concat-return-type"></a>

The array\_concat function returns a SUPER data value\.

## Example<a name="r_array_concat-example"></a>

The following examples shows concatenation of two arrays of the same type and concatenation of two arrays of different types\.

```
-- concatenating two arrays 
SELECT ARRAY_CONCAT(ARRAY(10001,10002),ARRAY(10003,10004));
              array_concat
------------------------------------
 [10001,10002,10003,10004]
(1 row)

-- concatenating two arrays of different types 
SELECT ARRAY_CONCAT(ARRAY(10001,10002),ARRAY('ab','cd'));
          array_concat
------------------------------
 [10001,10002,"ab","cd"]
(1 row)
```