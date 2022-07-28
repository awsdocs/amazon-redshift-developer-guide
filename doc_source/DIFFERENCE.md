# DIFFERENCE function<a name="DIFFERENCE"></a>

The DIFFERENCE function compares two strings by converting the strings to American Soundex codes and returning an INTEGER to indicate the difference between the codes\. 

## Syntax<a name="DIFFERENCE-synopsis"></a>

```
DIFFERENCE(string1, string2)
```

## Arguments<a name="DIFFERENCE-arguments"></a>

 *string1*   
You specify the first CHAR or VARCHAR string of two strings that you want the function to compare\. 

 *string2*   
You specify the second CHAR or VARCHAR string of two strings that you want the function to compare\. 

## Return type<a name="DIFFERENCE-return-type"></a>

The DIFFERENCE function returns an INTEGER value 0–4 that counts the number of different letters in the two strings compared as American Soundex codes\. The function returns 4 if the American Soundex code value is the same\.

## Usage notes<a name="r_DIFFERENCE_usage_notes"></a>

The DIFFERENCE function converts only English alphabetical lowercase or uppercase ASCII characters, including a–z and A–Z\. DIFFERENCE ignores other characters\. DIFFERENCE returns 0 if one of the two strings doesn't contain valid characters\. It returns 1 if neither string contains valid characters\. 

```
select difference('Amazon', '+-*/%');
```

```
  difference
------------
          0
```

## Example<a name="DIFFERENCE-examples"></a>

The following example returns 4 because the Soundex value for the two strings is the same\.

```
select difference('AC/DC', 'Ay See Dee See');
```

```
  difference
------------
          4
```