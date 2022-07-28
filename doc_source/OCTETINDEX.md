# OCTETINDEX function<a name="OCTETINDEX"></a>

The OCTETINDEX function returns the location of a substring within a string as a number of bytes\.

## Syntax<a name="OCTETINDEX-synopsis"></a>

```
OCTETINDEX(substring, string)
```

## Arguments<a name="OCTETINDEX-arguments"></a>

 *substring*   
You specify a CHAR or VARCHAR substring to locate within a string\. 

 *string*   
You specify a CHAR or VARCHAR string to search for the substring\. 

## Return type<a name="OCTETINDEX-return-type"></a>

The OCTETINDEX function returns an INTEGER value corresponding to the position of the substring within the string as a number of bytes, where the first character in the string is counted as 1\. If the string doesn't contain multibyte characters, the result is equal to the result of the CHARINDEX function\. 

## Examples<a name="OCTETINDEX-examples"></a>

The following example returns 8 because the substring AWS begins on the eighth byte of the string\.

```
select octetindex('AWS', 'Amazon AWS');
```

```
  octetindex
------------
          8
```

The following example returns 14 because the first six characters of the string are double\-byte characters\.

```
select octetindex('AWS', 'Άμαζον AWS');
```

```
  octetindex
------------
         14
```