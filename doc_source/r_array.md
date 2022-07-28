# array function<a name="r_array"></a>

Creates an array of the SUPER data type\.

## Syntax<a name="r_array-synopsis"></a>

```
ARRAY( [ expr1 ] [ , expr2 [ , ... ] ] )
```

## Argument<a name="r_array-argument"></a>

 *expr1, expr2*   
Expressions of any Amazon Redshift data type except date and time types, since Amazon Redshift doesn't cast the date and time types to the SUPER data type\. The arguments don't need to be of the same data type\.

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