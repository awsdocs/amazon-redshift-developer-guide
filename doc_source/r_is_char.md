# IS\_CHAR function<a name="r_is_char"></a>

Checks whether a value is a CHAR\. The IS\_CHAR function returns true for strings that have only ASCII characters, because the CHAR type can store only characters that are in the ASCII format\. The function returns false for any other values\.

## Syntax<a name="r_is_char-synopsis"></a>

```
IS_CHAR (super_expression)
```

## Arguments<a name="r_is_char-arguments"></a>

*super\_expression*  
A SUPER expression or column\.

## Returns<a name="r_is_char-returns"></a>

Boolean

## Example<a name="r_is_char_example"></a>

The following query shows an IS\_CHAR function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES ('t');

SELECT s, is_char(s) FROM t;

  s  | is_char
-----+---------
 "t" | True
(1 row);
```