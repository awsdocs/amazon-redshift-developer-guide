# CRC32 function<a name="crc32-function"></a>

CRC32 is an error\-detecting function that uses a CRC32 algorithm to detect changes between source and target data\. The CRC32 function converts a variable\-length string into an 8\-character string that is a text representation of the hexadecimal value of a 32 bit\-binary sequence\.

## Syntax<a name="crc32-function-syntax"></a>

```
CRC32(string)
```

## Arguments<a name="crc32-function-arguments"></a>

 *string*   
A variable\-length string\.

## Return type<a name="crc32-function-return-type"></a>

The CRC32 function returns an 8\-character string that is a text representation of the hexadecimal value of a 32\-bit binary sequence\. The Amazon Redshift CRC32 function is based on the CRC\-32C polynomial\. 

## Example<a name="crc32-function-example"></a>

The following example shows the 32\-bit value for the string 'Amazon Redshift': 

```
select crc32('Amazon Redshift');
crc32
----------------------------------
f2726906 
(1 row)
```