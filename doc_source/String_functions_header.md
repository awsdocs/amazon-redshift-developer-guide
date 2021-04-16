# String functions<a name="String_functions_header"></a>

**Topics**
+ [\|\| \(Concatenation\) operator](r_concat_op.md)
+ [ASCII function](r_ASCII.md)
+ [BPCHARCMP function](r_BPCHARCMP.md)
+ [BTRIM function](r_BTRIM.md)
+ [BTTEXT\_PATTERN\_CMP function](r_BTTEXT_PATTERN_CMP.md)
+ [CHAR\_LENGTH function](r_CHAR_LENGTH.md)
+ [CHARACTER\_LENGTH function](r_CHARACTER_LENGTH.md)
+ [CHARINDEX function](r_CHARINDEX.md)
+ [CHR function](r_CHR.md)
+ [CONCAT](r_CONCAT.md)
+ [CRC32 function](crc32-function.md)
+ [DIFFERENCE function](DIFFERENCE.md)
+ [INITCAP function](r_INITCAP.md)
+ [LEFT and RIGHT functions](r_LEFT.md)
+ [LEN function](r_LEN.md)
+ [LENGTH function](r_LENGTH.md)
+ [LOWER function](r_LOWER.md)
+ [LPAD and RPAD functions](r_LPAD.md)
+ [LTRIM function](r_LTRIM.md)
+ [OCTETINDEX function](OCTETINDEX.md)
+ [OCTET\_LENGTH function](r_OCTET_LENGTH.md)
+ [POSITION function](r_POSITION.md)
+ [QUOTE\_IDENT function](r_QUOTE_IDENT.md)
+ [QUOTE\_LITERAL function](r_QUOTE_LITERAL.md)
+ [REGEXP\_COUNT function](REGEXP_COUNT.md)
+ [REGEXP\_INSTR function](REGEXP_INSTR.md)
+ [REGEXP\_REPLACE function](REGEXP_REPLACE.md)
+ [REGEXP\_SUBSTR function](REGEXP_SUBSTR.md)
+ [REPEAT function](r_REPEAT.md)
+ [REPLACE function](r_REPLACE.md)
+ [REPLICATE function](r_REPLICATE.md)
+ [REVERSE function](r_REVERSE.md)
+ [RTRIM function](r_RTRIM.md)
+ [SOUNDEX function](SOUNDEX.md)
+ [SPLIT\_PART function](SPLIT_PART.md)
+ [STRPOS function](r_STRPOS.md)
+ [STRTOL function](r_STRTOL.md)
+ [SUBSTRING function](r_SUBSTRING.md)
+ [TEXTLEN function](r_TEXTLEN.md)
+ [TRANSLATE function](r_TRANSLATE.md)
+ [TRIM function](r_TRIM.md)
+ [UPPER function](r_UPPER.md)

String functions process and manipulate character strings or expressions that evaluate to character strings\. When the *string* argument in these functions is a literal value, it must be enclosed in single quotation marks\. Supported data types include CHAR and VARCHAR\. 

The following section provides the function names, syntax, and descriptions for supported functions\. All offsets into strings are one\-based\. 
<a name="string-functions-deprecated"></a>
**Deprecated leader node\-only functions**  
The following string functions are deprecated because they execute only on the leader node\. For more information, see [Leader node–only functions](c_SQL_functions_leader_node_only.md)
+ GET\_BIT
+ GET\_BYTE
+ SET\_BIT
+ SET\_BYTE
+ TO\_ASCII