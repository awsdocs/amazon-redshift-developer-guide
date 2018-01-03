# FLOOR Function<a name="r_FLOOR"></a>

The FLOOR function rounds a number down to the next whole number\. 

## Syntax<a name="r_FLOOR-synopsis"></a>

```
FLOOR (number)
```

## Argument<a name="r_FLOOR-argument"></a>

 *number*   
DOUBLE PRECISION number to be rounded down\. 

## Return Type<a name="r_FLOOR-return-type"></a>

FLOOR returns an integer\. 

## Example<a name="r_FLOOR-example"></a>

Calculate the floor of the commission paid for a given sales transaction: 

```
select floor(commission) from sales
where salesid=10000;

floor
-------
28
(1 row)
```