# FLOOR function<a name="r_FLOOR"></a>

The FLOOR function rounds a number down to the next whole number\. 

## Syntax<a name="r_FLOOR-synopsis"></a>

```
FLOOR (number)
```

## Argument<a name="r_FLOOR-argument"></a>

 *number*   
DOUBLE PRECISION number to be rounded down\. 

## Return type<a name="r_FLOOR-return-type"></a>

FLOOR returns an integer\. 

## Example<a name="r_FLOOR-example"></a>

The example shows the value of the commission paid for a given sales transaction before and after using the FLOOR function\. 

```
select commission from sales
where salesid=10000;

floor
-------
28.05
(1 row)

select floor(commission) from sales
where salesid=10000;

floor
-------
28
(1 row)
```