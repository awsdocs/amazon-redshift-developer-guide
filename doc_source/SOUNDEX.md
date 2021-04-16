# SOUNDEX function<a name="SOUNDEX"></a>

The SOUNDEX function returns the American Soundex value consisting of the first letter followed by a 3–digit encoding of the sounds that represent the English pronunciation of the string that you specify\. 

## Syntax<a name="SOUNDEX-synopsis"></a>

```
SOUNDEX(string)
```

## Arguments<a name="SOUNDEX-arguments"></a>

 *string*   
You specify a CHAR or VARCHAR string that you want to convert to an American Soundex code value\. 

## Return type<a name="SOUNDEX-return-type"></a>

The SOUNDEX function returns a VARCHAR\(4\) string consisting of a capital letter followed by a three–digit encoding of the sounds that represent the English pronunciation\.

## Usage notes<a name="r_SOUNDEX_usage_notes"></a>

The SOUNDEX function converts only English alphabetical lowercase and uppercase ASCII characters, including a–z and A–Z\. SOUNDEX ignores other characters\. SOUNDEX returns a single Soundex value for a string of multiple words separated by spaces\.

```
select soundex('AWS Amazon');
```

```
 soundex
---------
 A252
```

SOUNDEX returns an empty string if the input string doesn't contain any English letters\.

```
select soundex('+-*/%');
```

```
  soundex
---------
```

## Example<a name="SOUNDEX-examples"></a>

The following example returns the Soundex A525 for the word Amazon\.

```
select soundex('Amazon');
```

```
 soundex
---------
 A525
```