# VARBYTE type<a name="r_VARBYTE_type"></a>

Use a VARBYTE, VARBINARY, or BINARY VARYING column to store variable\-length binary value with a fixed limit\. 

```
varbyte [ (n) ]
```

The maximum number of bytes \(*n*\) can range from 1 – 1,024,000\. The default is 64,000\. 

Some examples where you might want to use a VARBYTE data type are as follows:
+ Joining tables on VARBYTE columns\.
+ Creating materialized views that contain VARBYTE columns\. Incremental refresh of materialized views that contain VARBYTE columns is supported\. However, aggregate functions other than COUNT, MIN, and MAX and GROUP BY on VARBYTE columns don't support incremental refresh\.

To ensure that all bytes are printable characters, Amazon Redshift uses the hex format to print VARBYTE values\. For example, the following SQL converts the hexadecimal string `6162` into a binary value\. Even though the returned value is a binary value, the results are printed as hexadecimal `6162`\.

```
select from_hex('6162');
                      
 from_hex
----------
 6162
```

Amazon Redshift supports casting between VARBYTE and the following data types:
+ CHAR
+ VARCHAR
+ SMALLINT
+ INTEGER
+ BIGINT

When casting with CHAR and VARCHAR the UTF\-8 format is used\. For more information about the UTF\-8 format, see [TO\_VARBYTE](r_TO_VARBYTE.md)\. When casting from SMALLINT, INTEGER, and BIGINT the number of bytes of the original data type is maintained\. That is two bytes for SMALLINT, four bytes for INTEGER, and eight bytes for BIGINT\.

The following SQL statement casts a VARCHAR string to a VARBYTE\. Even though the returned value is a binary value, the results are printed as hexadecimal `616263`\.

```
select 'abc'::varbyte;
                      
 varbyte
---------
 616263
```

The following SQL statement casts a CHAR value in a column to a VARBYTE\. This example creates a table with a CHAR\(10\) column \(c\), inserts character values that are shorter than the length of 10\. The resulting cast pads the result with a space characters \(hex'20'\) to the defined column size\. Even though the returned value is a binary value, the results are printed as hexadecimal\.

```
create table t (c char(10));
insert into t values ('aa'), ('abc');                 
select c::varbyte from t;
          c
----------------------
 61612020202020202020
 61626320202020202020
```

The following SQL statement casts a SMALLINT string to a VARBYTE\. Even though the returned value is a binary value, the results are printed as hexadecimal `0005`, which is two bytes or four hexadecimal characters\.

```
select 5::smallint::varbyte;
                  
 varbyte
---------
 0005
```

The following SQL statement casts an INTEGER to a VARBYTE\. Even though the returned value is a binary value, the results are printed as hexadecimal `00000005`, which is four bytes or eight hexadecimal characters\.

```
select 5::int::varbyte;
                  
 varbyte
----------
 00000005
```

The following SQL statement casts a BIGINT to a VARBYTE\. Even though the returned value is a binary value, the results are printed as hexadecimal `0000000000000005`, which is eight bytes or 16 hexadecimal characters\.

```
select 5::bigint::varbyte;
                  
     varbyte
------------------
 0000000000000005
```

Amazon Redshift features that support the VARBYTE data type include:
+ [VARBYTE operators](r_VARBYTE_OPERATORS.md)
+ [CONCAT](r_CONCAT.md)
+ [LEN](r_LEN.md)
+ [LENGTH function](r_LENGTH.md)
+ [OCTET\_LENGTH](r_OCTET_LENGTH.md)
+ [SUBSTR function](r_SUBSTR.md)
+ [SUBSTRING function](r_SUBSTRING.md)
+ [FROM\_HEX](r_FROM_HEX.md)
+ [TO\_HEX](r_TO_HEX.md)
+ [FROM\_VARBYTE](r_FROM_VARBYTE.md)
+ [TO\_VARBYTE](r_TO_VARBYTE.md)
+ [GETBIT](r_GETBIT.md)
+ [Loading a column of the VARBYTE data type](copy-usage-varbyte.md)
+ [Unloading a column of the VARBYTE data type](r_UNLOAD.md#unload-usage-notes)

## Limitations when using the VARBYTE data type with Amazon Redshift<a name="varbyte-limitations"></a>

The following are limitations when using the VARBYTE data type with Amazon Redshift: 
+ Amazon Redshift Spectrum doesn't support the VARBYTE data type\. Therefore, you can't create or alter an external table with a VARBYTE column\. 
+ Amazon Redshift query editor and Amazon Redshift query editor v2 don't yet fully support VARBYTE data type\. Therefore, use a different SQL client when working with VARBYTE expressions\. 

  As a workaround to use the query editor, if the length of your data is below 64 KB and the content is valid UTF\-8, you can cast the VARBYTE values to VARCHAR, for example:

  ```
  select to_varbyte('6162', 'hex')::varchar;
  ```
+ You can't use VARBYTE data types with Python or Lambda user\-defined functions \(UDFs\)\. 
+ You can't use a VARBYTE column as a sort key or a distribution key for an Amazon Redshift table\. 
+ You can't create a HLLSKETCH column from a VARBYTE column or use APPROXIMATE COUNT DISTINCT on a VARBYTE column\. 