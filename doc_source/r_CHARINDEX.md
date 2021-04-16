# CHARINDEX function<a name="r_CHARINDEX"></a>

Returns the location of the specified substring within a string\. Synonym of the STRPOS function\.

## Syntax<a name="r_CHARINDEX-synopsis"></a>

```
CHARINDEX( substring, string )
```

## Arguments<a name="charindex-arguments"></a>

 *substring*   
The substring to search for within the *string*\.

 *string*   
The string or column to be searched\.

## Return type<a name="charindex-return-type"></a>

The CHARINDEX function returns an integer corresponding to the position of the substring \(one\-based, not zero\-based\)\. The position is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\.

## Usage notes<a name="charindex-usage_notes"></a>

CHARINDEX returns 0 if the substring is not found within the `string`:

```
select charindex('dog', 'fish');

charindex
----------
0
(1 row)
```

## Examples<a name="sub-charindex-usage-notes-examples"></a>

The following example shows the position of the string `fish` within the word `dogfish`: 

```
select charindex('fish', 'dogfish');
 charindex 
---------- 
         4 
(1 row)
```

The following example returns the number of sales transactions with a COMMISSION over 999\.00 from the SALES table: 

```
select distinct charindex('.', commission), count (charindex('.', commission))
from sales where charindex('.', commission) > 4 group by charindex('.', commission)
order by 1,2;

charindex | count
----------+-------
5 |	629
(1 row)
```

See [STRPOS function](r_STRPOS.md) for details\. 