# IS\_DECIMAL function<a name="r_is_decimal"></a>

Checks whether a value is a DECIMAL\. The IS\_DECIMAL function returns true for numbers that are not floating points\. The function returns false for any other values, including null\. 

The IS\_DECIMAL function is a superset of IS\_BIGINT\.

## Syntax<a name="r_is_decimal-synopsis"></a>

```
IS_DECIMAL (super_expression)
```

## Arguments<a name="r_is_decimal-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_decimal-returns"></a>

Boolean

## Example<a name="r_is_decimal_example"></a>

The following query shows an IS\_DECIMAL function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (1.22);

SELECT s, is_decimal(s) FROM t;

  s   | is_decimal
------+------------
 1.22 | True
(1 row)
```