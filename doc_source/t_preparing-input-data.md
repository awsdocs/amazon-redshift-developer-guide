# Preparing your input data<a name="t_preparing-input-data"></a>

If your input data is not compatible with the table columns that will receive it, the COPY command will fail\.

Use the following guidelines to help ensure that your input data is valid: 
+ Your data can only contain UTF\-8 characters up to four bytes long\.
+ Verify that CHAR and VARCHAR strings are no longer than the lengths of the corresponding columns\. VARCHAR strings are measured in bytes, not characters, so, for example, a four\-character string of Chinese characters that occupy four bytes each requires a VARCHAR\(16\) column\.
+ Multibyte characters can only be used with VARCHAR columns\. Verify that multibyte characters are no more than four bytes long\.
+ Verify that data for CHAR columns only contains single\-byte characters\.
+ Do not include any special characters or syntax to indicate the last field in a record\. This field can be a delimiter\.
+ If your data includes null terminators, also referred to as NUL \(UTF\-8 0000\) or binary zero \(0x000\), you can load these characters as NULLS into CHAR or VARCHAR columns by using the NULL AS option in the COPY command: `null as '\0'` or `null as '\000'` \. If you do not use NULL AS, null terminators will cause your COPY to fail\.
+ If your strings contain special characters, such as delimiters and embedded newlines, use the ESCAPE option with the [COPY](r_COPY.md) command\.
+ Verify that all single and double quotation marks are appropriately matched\.
+ Verify that floating\-point strings are in either standard floating\-point format, such as 12\.123, or an exponential format, such as 1\.0E4\.
+ Verify that all timestamp and date strings follow the specifications for [ DATEFORMAT and TIMEFORMAT strings](r_DATEFORMAT_and_TIMEFORMAT_strings.md)\. The default timestamp format is YYYY\-MM\-DD hh:mm:ss, and the default date format is YYYY\-MM\-DD\.
+ For more information about boundaries and limitations on individual data types, see [Data types](c_Supported_data_types.md)\. For information about multibyte character errors, see [Multibyte character load errors](multi-byte-character-load-errors.md)