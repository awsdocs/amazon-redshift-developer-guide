# LOG function<a name="r_LOG"></a>

Returns the base 10 logarithm of a number\. 

Synonym of [DLOG10 function](r_DLOG10.md)\. 

## Syntax<a name="r_LOG-synopsis"></a>

```
LOG(number)
```

## Argument<a name="r_LOG-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_LOG-return-type"></a>

The LOG function returns a double precision number\. 

## Example<a name="r_LOG-example"></a>

The following example returns the base 10 logarithm of the number 100: 

```
select log(100);
dlog10
--------
2
(1 row)
```