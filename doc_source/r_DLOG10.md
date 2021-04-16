# DLOG10 function<a name="r_DLOG10"></a>

The DLOG10 returns the base 10 logarithm of the input parameter\. Synonym of the LOG function\. 

Synonym of [LOG function](r_LOG.md)\. 

## Syntax<a name="r_DLOG10-synopsis"></a>

```
DLOG10(number)
```

## Argument<a name="r_DLOG10-argument"></a>

 *number*   
The input parameter is a double precision number\. 

## Return type<a name="r_DLOG10-return-type"></a>

The DLOG10 function returns a double precision number\. 

## Example<a name="r_DLOG10-example"></a>

The following example returns the base 10 logarithm of the number 100: 

```
select dlog10(100);

dlog10
--------
2
(1 row)
```