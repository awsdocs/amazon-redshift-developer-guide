# Multibyte character load errors<a name="multi-byte-character-load-errors"></a>

Columns with a CHAR data type only accept single\-byte UTF\-8 characters, up to byte value 127, or 7F hex, which is also the ASCII character set\. VARCHAR columns accept multibyte UTF\-8 characters, to a maximum of four bytes\. For more information, see [Character types](r_Character_types.md)\. 

If a line in your load data contains a character that is invalid for the column data type, COPY returns an error and logs a row in the STL\_LOAD\_ERRORS system log table with error number 1220\. The ERR\_REASON field includes the byte sequence, in hex, for the invalid character\. 

An alternative to fixing invalid characters in your load data is to replace the invalid characters during the load process\. To replace invalid UTF\-8 characters, specify the ACCEPTINVCHARS option with the COPY command\. For more information, see [ACCEPTINVCHARS](copy-parameters-data-conversion.md#acceptinvchars)\.

The following example shows the error reason when COPY attempts to load UTF\-8 character e0 a1 c7a4 into a CHAR column:

```
Multibyte character not supported for CHAR 
(Hint: Try using  VARCHAR). Invalid char: e0 a1 c7a4
```

If the error is related to a VARCHAR data type, the error reason includes an error code as well as the invalid UTF\-8 hex sequence\. The following example shows the error reason when COPY attempts to load UTF\-8 a4 into a VARCHAR field:

```
String contains invalid or unsupported UTF-8 codepoints. 
Bad UTF-8 hex sequence: a4 (error 3)
```

The following table lists the descriptions and suggested workarounds for VARCHAR load errors\. If one of these errors occurs, replace the character with a valid UTF\-8 code sequence or remove the character\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/multi-byte-character-load-errors.html)