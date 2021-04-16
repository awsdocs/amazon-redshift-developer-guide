# is\_char function<a name="r_is_char"></a>

Checks whether a value is a CHAR\. The is\_char function returns true for strings that have only ASCII characters, because Amazon Redshift can store only characters that are in the ASCII format\. The function returns false for any other values\.

## Example<a name="r_is_char_example"></a>

The following query shows an is\_char function\.

```
CREATE TABLE t(s super);

INSERT INTO t VALUES ('t');

SELECT s, is_char(s) FROM t;

  s  | is_char
-----+---------
 "t" | True
(1 row);
```