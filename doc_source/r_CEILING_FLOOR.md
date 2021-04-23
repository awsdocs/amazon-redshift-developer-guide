# CEILING \(or CEIL\) function<a name="r_CEILING_FLOOR"></a>

The CEILING or CEIL function is used to round a number up to the next whole number\. \(The [FLOOR function](r_FLOOR.md) rounds a number down to the next whole number\.\) 

## Syntax<a name="r_CEILING_FLOOR-synopsis"></a>

```
CEIL | CEILING(number)
```

## Arguments<a name="r_CEILING_FLOOR-arguments"></a>

 *number*   
The number or expression that evaluates to a number\. It can be the SMALLINT, INTEGER, BIGINT, DECIMAL, FLOAT4, FLOAT8, or SUPER type\.

## Return type<a name="r_CEILING_FLOOR-return-type"></a>

CEILING and CEIL return the same data type as its argument\. 

When the input is of the SUPER type, the output retains the same dynamic type as the input while the static type remains the SUPER type\. When the dynamic type of SUPER isn't a number, Amazon Redshift returns a null\.

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