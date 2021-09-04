# array function<a name="r_array"></a>

Creates an array of a specified type\.

## Syntax<a name="r_array-synopsis"></a>

```
ARRAY( INT (type) )
```

## Argument<a name="r_array-argument"></a>

 *type*   
The input expression must be a INT1, INT2, INT4, INT8, DATE, TIME, TIMESTAMP, VARCHAR, NVARCHAR, FLOAT, DOUBLE, or TIMETZ data type\.

## Return type<a name="r_array-return-type"></a>

The array function returns the SUPER data type\.

## Example<a name="r_array-example"></a>

The following examples show an array of numeric values and an array of different data types\.

```
--an array of numeric values
select array(1,50,null,100);
      array
------------------
 [1,50,null,100]
(1 row)

--an array of different data types
select array(1,'abc',true,3.14);
        array
-----------------------
 [1,"abc",true,3.14]
(1 row)
```