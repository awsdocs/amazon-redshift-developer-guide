# MURMUR3\_32\_HASH<a name="MURMUR3_32_HASH"></a>

The MURMUR3\_32\_HASH function computes the 32\-bit Murmur3A non\-cryptographic hash for all common data types including numeric and string types\.

## Syntax<a name="MURMUR3_32_HASH-syntax"></a>

```
MURMUR3_32_HASH(value [, seed])
```

## Arguments<a name="MURMUR3_32_HASH-arguments"></a>

 *value*   
The input value to hash\. Amazon Redshift hashes the binary representation of the input value\. This behavior is similar to [FNV\_HASH function](r_FNV_HASH.md), but the value is converted to the binary representation specified by the [Apache Iceberg 32\-bit Murmur3 hash specification](https://iceberg.apache.org/spec/#appendix-b-32-bit-hash-requirements)\.

 *seed*   
The INT seed of the hash function\. This argument is optional\. If not given, Amazon Redshift uses the default seed of 0\. This enables combining the hash of multiple columns without any conversions or concatenations\.

## Return type<a name="MURMUR3_32_HASH-return-type"></a>

The function returns an INT\.

## Example<a name="MURMUR3_32_HASH-example"></a>

The following examples return the Murmur3 hash of a number, the string 'Amazon Redshift', and the concatenation of the two\.

```
select MURMUR3_32_HASH(1);
    
    MURMUR3_32_HASH
----------------------
 -5968735742475085980
(1 row)
```

```
select MURMUR3_32_HASH('Amazon Redshift');
    
    MURMUR3_32_HASH
----------------------
 7783490368944507294
(1 row)
```

```
select MURMUR3_32_HASH('Amazon Redshift', MURMUR3_32_HASH(1));
    
    MURMUR3_32_HASH
----------------------
 -2202602717770968555
(1 row)
```

## Usage notes<a name="MURMUR3_32_HASH-usage-notes"></a>

To compute the hash of a table with multiple columns, you can compute the Murmur3 hash of the first column and pass it as a seed to the hash of the second column\. Then, it passes the Murmur3 hash of the second column as a seed to the hash of the third column\.

The following example creates seeds to hash a table with multiple columns\.

```
select MURMUR3_32_HASH(column_3, MURMUR3_32_HASH(column_2, MURMUR3_32_HASH(column_1))) from sample_table;
```

The same property can be used to compute the hash of a concatenation of strings\.

```
select MURMUR3_32_HASH('abcd');
   
   MURMUR3_32_HASH
---------------------
 -281581062704388899
(1 row)
```

```
select MURMUR3_32_HASH('cd', MURMUR3_32_HASH('ab'));
   
   MURMUR3_32_HASH
---------------------
 -281581062704388899
(1 row)
```

The hash function uses the type of the input to determine the number of bytes to hash\. Use casting to enforce a specific type, if necessary\.

The following examples use different input types to produce different results\.

```
select MURMUR3_32_HASH(1::smallint);
   
   MURMUR3_32_HASH
--------------------
 589727492704079044
(1 row)
```

```
select MURMUR3_32_HASH(1);
   
   MURMUR3_32_HASH
----------------------
 -5968735742475085980
(1 row)
```

```
select MURMUR3_32_HASH(1::bigint);
   
   MURMUR3_32_HASH
----------------------
 -8517097267634966620
(1 row)
```