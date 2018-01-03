# FUNC\_SHA1 Function<a name="FUNC_SHA1"></a>

The FUNC\_SHA1 function uses the SHA1 cryptographic hash function to convert a variable\-length string into a 40\-character string that is a text representation of the hexadecimal value of a 160\-bit checksum\.

## Syntax<a name="FUNC_SHA1-syntax"></a>

```
FUNC_SHA1(string)
```

## Arguments<a name="FUNC_SHA1-arguments"></a>

 *string*   
A variable\-length string\.

## Return Type<a name="FUNC_SHA1-returm-type"></a>

The FUNC\_SHA1 function returns a 40\-character string that is a text representation of the hexadecimal value of a 160\-bit checksum\. 

## Example<a name="FUNC_SHA1-example"></a>

The following example returns the 160\-bit value for the word 'Amazon Redshift': 

```
select func_sha1('Amazon Redshift');
```