# STRPOS function<a name="r_STRPOS"></a>

Returns the position of a substring within a specified string\. 

Synonym of [CHARINDEX function](r_CHARINDEX.md) and [POSITION function](r_POSITION.md)\. 

## Syntax<a name="r_STRPOS-synopsis"></a>

```
STRPOS(string, substring )
```

## Arguments<a name="r_STRPOS-arguments"></a>

 *string*   
The first input parameter is the string to be searched\. 

 *substring*   
The second parameter is the substring to search for within the *string*\. 

## Return type<a name="r_STRPOS-return-type"></a>

The STRPOS function returns an integer corresponding to the position of the substring \(one\-based, not zero\-based\)\. The position is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\.

## Usage notes<a name="r_STRPOS_usage_notes"></a>

STRPOS returns 0 if the *substring* is not found within the *string*: 

```
select strpos('dogfish', 'fist');
strpos
--------
0
(1 row)
```

## Examples<a name="r_STRPOS-examples"></a>

The following example shows the position of the string `fish` within the word `dogfish`: 

```
select strpos('dogfish', 'fish');
strpos
--------
4
(1 row)
```

The following example returns the number of sales transactions with a COMMISSION over 999\.00 from the SALES table: 

```
select distinct strpos(commission, '.'),
count (strpos(commission, '.'))
from sales
where strpos(commission, '.') > 4
group by strpos(commission, '.')
order by 1, 2;

strpos | count
--------+-------
5 |   629
(1 row)
```