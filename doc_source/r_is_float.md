# IS\_FLOAT function<a name="r_is_float"></a>

Checks whether a value is a floating point number\. The IS\_FLOAT function returns true for floating point numbers \(FLOAT4 and FLOAT8\)\. The function returns false for any other values\.

The set of IS\_DECIMAL the set of IS\_FLOAT are disjoint\.

## Syntax<a name="r_is_float-synopsis"></a>

```
IS_FLOAT (super_expression)
```

## Arguments<a name="r_is_float-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_float-returns"></a>

Boolean

## Example<a name="r_is_float_example"></a>

The following query shows an IS\_FLOAT function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES(2.22::FLOAT);

SELECT s, is_float(s) FROM t;

    s    | is_float
---------+----------
 2.22e+0 | True
(1 row)
```