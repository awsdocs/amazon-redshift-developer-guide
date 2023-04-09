# IS\_BIGINT function<a name="r_is_bigint"></a>

Checks whether a value is a BIGINT\. The IS\_BIGINT function returns true for numbers of scale 0 in the 64\-bit range\. Otherwise, the function returns false for all other values, including null and floating point numbers\.

The IS\_BIGINT function is a superset of IS\_INTEGER\.

## Syntax<a name="r_is_bigint-synopsis"></a>

```
IS_BIGINT (super_expression)
```

## Arguments<a name="r_is_bigint-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_bigint-returns"></a>

Boolean

## Example<a name="r_is_bigint_example"></a>

The following query shows an IS\_BIGINT function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (5);

SELECT s, is_bigint(s) FROM t;

 s  | is_bigint
----+-----------
 5  |   True
(1 row)
```