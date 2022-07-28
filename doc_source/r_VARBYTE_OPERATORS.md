# VARBYTE operators<a name="r_VARBYTE_OPERATORS"></a>

 The following table lists the VARBYTE operators\. The operator works with binary value of data type VARBYTE\. If one or both inputs is null, the result is null\.

## Supported operators<a name="r_VARBYTE_OPERATORS-supported-operators"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_VARBYTE_OPERATORS.html)

## Examples<a name="r_VARBYTE_OPERATORS-examples"></a>

In the following examples, the value of `'a'::varbyte` is `61` and the value of `'b'::varbyte` is `62`\.

The following example concatenates two binary values\.

```
select 'a'::varbyte || 'b'::varbyte as concat;
               
 concat
--------
 6162
```

The following example concatenates two binary values\.

```
select 'a'::varbyte + 'b'::varbyte as concat;
               
 concat
--------
 6162
```

The following example compares if `'a'` is less than `'b'`\.

```
select 'a'::varbyte < 'b'::varbyte as less_than;
               
less_than
-----------
 t
```

The following example compares if `'a'` equals `'b'`\.

```
select 'a'::varbyte = 'b'::varbyte as equal;
               
equal
-----------
 f
```

The following example negates each bit of the input binary value\. Where `'a'` evaluates to `01100001`\.

```
select from_varbyte(~'a'::varbyte, 'binary');
               
from_varbyte
--------------
 10011110
```

The following example applies the `bitwise and` operator on the two input binary values\. Where `'a'` evaluates to `01100001` and `'b'` evaluates to `01100010`\.

```
select from_varbyte('a'::varbyte & 'b'::varbyte, 'binary');
               
from_varbyte
--------------
 01100000
```