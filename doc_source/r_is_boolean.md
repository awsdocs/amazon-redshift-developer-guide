# IS\_BOOLEAN function<a name="r_is_boolean"></a>

Checks whether a value is a BOOLEAN\. The IS\_BOOLEAN function returns true for constant JSON Booleans\. The function returns false for any other values, including null\.

## Syntax<a name="r_is_boolean-synopsis"></a>

```
IS_BOOLEAN (super_expression)
```

## Arguments<a name="r_is_boolean-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_boolean-returns"></a>

Boolean

## Example<a name="r_is_boolean_example"></a>

The following query shows an IS\_BOOLEAN function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES (TRUE);

SELECT s, is_boolean(s) FROM t;
   s    | is_boolean
--------+-----------
 "true" |   True
(1 row)
```