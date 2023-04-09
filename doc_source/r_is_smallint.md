# IS\_SMALLINT function<a name="r_is_smallint"></a>

Checks whether a variable is a SMALLINT\. The IS\_SMALLINT function returns true for numbers of scale 0 in the 16\-bit range\. The function returns false for any other values, including null and floating point numbers\.

## Syntax<a name="r_is_smallint-synopsis"></a>

```
IS_SMALLINT (super_expression)
```

## Arguments<a name="r_is_smallint-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_smallint-returns"></a>

Boolean

## Example<a name="r_is_smallint_example"></a>

The following query shows an IS\_SMALLINT function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (5);

SELECT s, is_smallint(s) FROM t;

 s | is_smallint
---+-------------
 5 | True
(1 row)
```