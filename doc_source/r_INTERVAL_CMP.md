# INTERVAL\_CMP function<a name="r_INTERVAL_CMP"></a>

INTERVAL\_CMP compares two intervals and returns `1` if the first interval is greater, `-1` if the second interval is greater, and `0` if the intervals are equal\. For more information, see [Interval literals](r_interval_literals.md)\.

## Syntax<a name="r_INTERVAL_CMP-syntax"></a>

```
INTERVAL_CMP(interval1, interval2)
```

## Arguments<a name="r_INTERVAL_CMP-arguments"></a>

 *interval1*   
An interval literal value\.

 *interval2*   
An interval literal value\.

## Return type<a name="r_INTERVAL_CMP-return-type"></a>

INTEGER

## Examples<a name="r_INTERVAL_CMP-examples"></a>

The following example compares the value of "3 days" to "1 year"\. 

```
select interval_cmp('3 days','1 year');

interval_cmp
--------------
-1
```

This example compares the value "7 days" to "1 week"\. 

```
select interval_cmp('7 days','1 week');

interval_cmp
--------------
0
(1 row)
```