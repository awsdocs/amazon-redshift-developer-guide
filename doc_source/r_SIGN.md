# SIGN function<a name="r_SIGN"></a>

 The SIGN function returns the sign \(positive or negative\) of a number\. The result of the SIGN function is `1`, `-1`, or `0` indicating the sign of the argument\. 

## Syntax<a name="r_SIGN-synopsis"></a>

```
SIGN (number)
```

## Argument<a name="r_SIGN-argument"></a>

 *number*   
Number to be evaluated\. The data type can be `numeric` or `double precision`\. Other data types can be converted by Amazon Redshift per the implicit conversion rules\.

## Return type<a name="r_SIGN-return-type"></a>

The output type is `numeric(1, 0)` for a numeric input, and `double precision` for a double precision input\.

## Examples<a name="r_SIGN-examples"></a>

Determine the sign of the commission paid for a given transaction: 

```
select commission, sign (commission)
from sales where salesid=10000;

commission | sign
-----------+------
     28.05 |    1
(1 row)
```

The following example shows that “t2\.d” has double precision as its type since the input is double precision and that “t2\.n” has numeric\(1,0\) as output since the input is numeric\. 

```
CREATE TABLE t1(d double precision, n numeric(12, 2));
INSERT INTO t1 VALUES (4.25, 4.25), (-4.25, -4.25);
CREATE TABLE t2 AS SELECT SIGN(d) AS d, SIGN(n) AS n FROM t1;
\d t1
\d t2
 
The output of the “\d” commands is:
                                      Table "public.t1"
Column |       Type       | Encoding | DistKey | SortKey | Preload | Encryption | Modifiers
--------+------------------+----------+---------+---------+---------+------------+-----------
d      | double precision | none     | f       | 0       | f       | none       |
n      | numeric(12,2)    | lzo      | f       | 0       | f       | none       |
 
                                      Table "public.t2"
Column |       Type       | Encoding | DistKey | SortKey | Preload | Encryption | Modifiers
--------+------------------+----------+---------+---------+---------+------------+-----------
d      | double precision | none     | f       | 0       | f       | none       |
n      | numeric(1,0)     | lzo      | f       | 0       | f       | none       |
```