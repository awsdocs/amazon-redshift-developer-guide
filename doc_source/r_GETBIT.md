# GETBIT function<a name="r_GETBIT"></a>

GETBIT returns the bit value of a binary value at the specified by an index\. 

## Syntax<a name="r_GETBIT-synopsis"></a>

```
getbit(binary_value, index)
```

## Arguments<a name="r_GETBIT-arguments"></a>

 *binary\_value*   
A binary value of data type VARBYTE\. 

 *index*   
An index number of the bit in the binary value that is returned\. The binary value is a 0\-based bit array that is indexed from the rightmost bit \(least significant bit\) to the leftmost bit \(most significant bit\)\.

## Return type<a name="r_GETBIT-return-type"></a>

INTEGER

## Examples<a name="r_GETBIT-examples"></a>

The following example returns the bit at index `2` of the binary value `from_hex('4d')`\. The binary representation of '4d' is `01001101`\.

```
select getbit(from_hex('4d'), 2);
               
getbit
--------
      1
```

The following example returns the bit at eight index locations of the binary value returned by `from_hex('4d')`\. The binary representation of '4d' is `01001101`\.

```
select getbit(from_hex('4d'), 7), getbit(from_hex('4d'), 6),
  getbit(from_hex('4d'), 5), getbit(from_hex('4d'), 4),
  getbit(from_hex('4d'), 3), getbit(from_hex('4d'), 2),
  getbit(from_hex('4d'), 1), getbit(from_hex('4d'), 0);
               
 getbit | getbit | getbit | getbit | getbit | getbit | getbit | getbit
--------+--------+--------+--------+--------+--------+--------+--------
      0 |      1 |      0 |      0 |      1 |      1 |      0 |      1
```