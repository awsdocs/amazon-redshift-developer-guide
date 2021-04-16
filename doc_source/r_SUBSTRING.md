# SUBSTRING function<a name="r_SUBSTRING"></a>

Returns the characters extracted from a string based on the specified character position for a specified number of characters\. 

The character position and number of characters are based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\. You cannot specify a negative length, but you can specify a negative starting position\.

## Syntax<a name="r_SUBSTRING-synopsis"></a>

```
SUBSTRING(string FROM start_position [ FOR number_characters ] )
```

```
SUBSTRING(string, start_position, number_characters )
```

## Arguments<a name="r_SUBSTRING-arguments"></a>

 *string*   
The string to be searched\. Non\-character data types are treated like a string\. 

 *start\_position*   
The position within the string to begin the extraction, starting at 1\. The *start\_position* is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\. This number can be negative\.

 *number\_characters*   
The number of characters to extract \(the length of the substring\)\. The *number\_characters* is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\. This number cannot be negative\.

## Return type<a name="r_SUBSTRING-return-type"></a>

VARCHAR

## Usage notes<a name="r_SUBSTRING_usage_notes"></a>

The following example returns a four\-character string beginning with the sixth character\. 

```
select substring('caterpillar',6,4);
substring
-----------
pill
(1 row)
```

If the *start\_position* \+ *number\_characters* exceeds the length of the *string*, SUBSTRING returns a substring starting from the *start\_position* until the end of the string\. For example: 

```
select substring('caterpillar',6,8);
substring
-----------
pillar
(1 row)
```

If the `start_position` is negative or 0, the SUBSTRING function returns a substring beginning at the first character of string with a length of `start_position` \+ `number_characters` \-1\. For example:

```
select substring('caterpillar',-2,6);
substring
-----------
cat
(1 row)
```

If `start_position` \+ `number_characters` \-1 is less than or equal to zero, SUBSTRING returns an empty string\. For example:

```
select substring('caterpillar',-5,4);
substring
-----------

(1 row)
```

## Examples<a name="r_SUBSTRING-examples"></a>

The following example returns the month from the LISTTIME string in the LISTING table: 

```
select listid, listtime,
substring(listtime, 6, 2) as month
from listing
order by 1, 2, 3
limit 10;

 listid |      listtime       | month
--------+---------------------+-------
      1 | 2008-01-24 06:43:29 | 01
      2 | 2008-03-05 12:25:29 | 03
      3 | 2008-11-01 07:35:33 | 11
      4 | 2008-05-24 01:18:37 | 05
      5 | 2008-05-17 02:29:11 | 05
      6 | 2008-08-15 02:08:13 | 08
      7 | 2008-11-15 09:38:15 | 11
      8 | 2008-11-09 05:07:30 | 11
      9 | 2008-09-09 08:03:36 | 09
     10 | 2008-06-17 09:44:54 | 06
(10 rows)
```

The following example is the same as above, but uses the FROM\.\.\.FOR option: 

```
select listid, listtime,
substring(listtime from 6 for 2) as month
from listing
order by 1, 2, 3
limit 10;

 listid |      listtime       | month
--------+---------------------+-------
      1 | 2008-01-24 06:43:29 | 01
      2 | 2008-03-05 12:25:29 | 03
      3 | 2008-11-01 07:35:33 | 11
      4 | 2008-05-24 01:18:37 | 05
      5 | 2008-05-17 02:29:11 | 05
      6 | 2008-08-15 02:08:13 | 08
      7 | 2008-11-15 09:38:15 | 11
      8 | 2008-11-09 05:07:30 | 11
      9 | 2008-09-09 08:03:36 | 09
     10 | 2008-06-17 09:44:54 | 06
(10 rows)
```

You cannot use SUBSTRING to predictably extract the prefix of a string that might contain multi\-byte characters because you need to specify the length of a multi\-byte string based on the number of bytes, not the number of characters\. To extract the beginning segment of a string based on the length in bytes, you can CAST the string as VARCHAR\(*byte\_length*\) to truncate the string, where *byte\_length* is the required length\. The following example extracts the first 5 bytes from the string `'Fourscore and seven'`\.

```
select cast('Fourscore and seven' as varchar(5));

varchar
-------
Fours
```