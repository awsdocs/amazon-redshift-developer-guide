# SHA2 function<a name="SHA2"></a>

The SHA2 function uses the SHA2 cryptographic hash function to convert a variable\-length string into a character string\. The character string is a text representation of the hexadecimal value of the checksum with the specified number of bits\.

## Syntax<a name="SHA2-syntax"></a>

```
SHA2(string, bits)
```

## Arguments<a name="SHA2-arguments"></a>

 *string*   
A variable\-length string\.

 *integer*   
The number of bits in the hash functions\. Valid values are 0 \(same as 256\), 224, 256, 384, and 512\.

## Return type<a name="SHA2-returm-type"></a>

The SHA2 function returns a character string that is a text representation of the hexadecimal value of the checksum or an empty string if the number of bits is invalid\. 

## Example<a name="SHA2-example"></a>

The following example returns the 256\-bit value for the word 'Amazon Redshift': 

```
select sha2('Amazon Redshift', 256);
```