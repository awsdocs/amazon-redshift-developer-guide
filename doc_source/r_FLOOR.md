# FLOOR function<a name="r_FLOOR"></a>

The FLOOR function rounds a number down to the next whole number\. 

## Syntax<a name="r_FLOOR-synopsis"></a>

```
FLOOR (number)
```

## Argument<a name="r_FLOOR-argument"></a>

 *number*   
The number or expression that evaluates to a number\. It can be the SMALLINT, INTEGER, BIGINT, DECIMAL, FLOAT4, FLOAT8, or SUPER type\. 

## Return type<a name="r_FLOOR-return-type"></a>

FLOOR returns the same data type as its argument\. 

When the input is of the SUPER type, the output retains the same dynamic type as the input while the static type remains the SUPER type\. When the dynamic type of SUPER isn't a number, Amazon Redshift returns a null\.

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