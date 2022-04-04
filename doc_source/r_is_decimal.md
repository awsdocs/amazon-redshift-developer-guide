# is\_decimal function<a name="r_is_decimal"></a>

Checks whether a value is a decimal\. The is\_decimal function returns true for numbers that are not floating points\. The function returns false for any other values, including null\. 

The is\_decimal function is a superset of is\_bigint\.

## Syntax<a name="r_is_decimal-synopsis"></a>

```
is_decimal (super_expression)
```

## Arguments<a name="r_is_decimal-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_decimal-returns"></a>

Boolean

## Example<a name="r_is_decimal_example"></a>

The following query shows an is\_decimal function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (1.22);

SELECT s, is_decimal(s) FROM t;

  s   | is_decimal
------+------------
 1.22 | True
(1 row)
```