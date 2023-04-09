# IS\_SCALAR function<a name="r_is_scalar"></a>

Checks whether a variable is a scalar\. The IS\_SCALAR function returns true for any value that is not an array or an object\. The function returns false for any other values, including null\.

The set of IS\_ARRAY, IS\_OBJECT, and IS\_SCALAR cover all values except nulls\.

## Syntax<a name="r_is_scalar-synopsis"></a>

```
IS_SCALAR (super_expression)
```

## Arguments<a name="r_is_scalar-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_scalar-returns"></a>

Boolean

## Example<a name="r_is_scalar_example"></a>

The following query shows an IS\_SCALAR function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (JSON_PARSE('{"name": "Joe"}'));

SELECT s, is_scalar(s.name) FROM t;

       s        | is_scalar
----------------+-----------
 {"name":"Joe"} | True
(1 row)
```