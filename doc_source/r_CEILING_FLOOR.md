# CEILING \(or CEIL\) function<a name="r_CEILING_FLOOR"></a>

The CEILING or CEIL function is used to round a number up to the next whole number\. \(The [FLOOR function](r_FLOOR.md) rounds a number down to the next whole number\.\) 

## Syntax<a name="r_CEILING_FLOOR-synopsis"></a>

```
CEIL | CEILING(number)
```

## Arguments<a name="r_CEILING_FLOOR-arguments"></a>

 *number*   
DOUBLE PRECISION number to be rounded\. 

## Return type<a name="r_CEILING_FLOOR-return-type"></a>

CEILING and CEIL return an integer\. 

## Example<a name="r_CEILING_FLOOR-example"></a>

Calculate the ceiling of the commission paid for a given sales transaction: 

```
select ceiling(commission) from sales
where salesid=10000;

ceiling
---------
29
(1 row)
```