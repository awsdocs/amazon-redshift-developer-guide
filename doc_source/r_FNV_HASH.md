# FNV\_HASH function<a name="r_FNV_HASH"></a>

Computes the 64\-bit FNV\-1a non\-cryptographic hash function for all basic data types\.

## Syntax<a name="r_FNV_HASH-synopsis"></a>

```
FNV_HASH(value [, seed])
```

## Arguments<a name="r_FNV_HASH-argument-arguments"></a>

 *value*  
The input value to be hashed\. Amazon Redshift uses the binary representation of the value to hash the input value; for instance, INTEGER values are hashed using 4 bytes and BIGINT values are hashed using 8 bytes\. Also, hashing CHAR and VARCHAR inputs does not ignore trailing spaces\.

 *seed*  
The BIGINT seed of the hash function is optional\. If not given, Amazon Redshift uses the default FNV seed\. This enables combining the hash of multiple columns without any conversions or concatenations\.

## Return type<a name="r_FNV_HASH-return-type"></a>

BIGINT

## Example<a name="r_FNV_HASH-example"></a>

The following examples return the FNV hash of a number, the string 'Amazon Redshift', and the concatenation of the two\.

```
select fnv_hash(1);
        fnv_hash
----------------------
 -5968735742475085980
(1 row)
```

```
select fnv_hash('Amazon Redshift');
      fnv_hash
---------------------
 7783490368944507294
(1 row)
```

```
select fnv_hash('Amazon Redshift', fnv_hash(1));
       fnv_hash
----------------------
 -2202602717770968555
(1 row)
```

## Usage notes<a name="r_FNV_HASH-usage-notes"></a>
+ To compute the hash of a table with multiple columns, you can compute the FNV hash of the first column and pass it as a seed to the hash of the second column\. Then, it passes the FNV hash of the second column as a seed to the hash of the third column\.

  The following example creates seeds to hash a table with multiple columns\. 

  ```
  select fnv_hash(column_3, fnv_hash(column_2, fnv_hash(column_1))) from sample_table;
  ```
+ The same property can be used to compute the hash of a concatenation of strings\. 

  ```
  select fnv_hash('abcd');
         fnv_hash
  ---------------------
   -281581062704388899
  (1 row)
  ```

  ```
  select fnv_hash('cd', fnv_hash('ab'));
        fnv_hash
  ---------------------
   -281581062704388899
  (1 row)
  ```
+ The hash function uses the type of the input to determine the number of bytes to hash\. Use casting to enforce a specific type, if necessary\.

  The following examples use different types of input to produce different results\.

  ```
  select fnv_hash(1::smallint);
        fnv_hash
  --------------------
   589727492704079044
  (1 row)
  ```

  ```
  select fnv_hash(1);
         fnv_hash
  ----------------------
   -5968735742475085980
  (1 row)
  ```

  ```
  select fnv_hash(1::bigint);
         fnv_hash
  ----------------------
   -8517097267634966620
  (1 row)
  ```