# BTRIM function<a name="r_BTRIM"></a>

The BTRIM function trims a string by removing leading and trailing blanks or by removing characters that match an optional specified string\. 

## Syntax<a name="r_BTRIM-synopsis"></a>

```
BTRIM(string [, matching_string ] )
```

## Arguments<a name="r_BTRIM-arguments"></a>

 *string*   
The first input parameter is a VARCHAR string\. 

 *matching\_string*   
The second parameter, if present, is a VARCHAR string\. 

## Return type<a name="r_BTRIM-return-type"></a>

The BTRIM function returns a VARCHAR string\. 

## Examples<a name="r_BTRIM-examples"></a>

The following example trims leading and trailing blanks from the string `' abc '`: 

```
select '     abc    ' as untrim, btrim('     abc    ') as trim;

untrim    | trim
----------+------
  abc     | abc
(1 row)
```

The following example removes the leading and trailing `'xyz'` strings from the string `'xyzaxyzbxyzcxyz'` 

```
select 'xyzaxyzbxyzcxyz' as untrim,
btrim('xyzaxyzbxyzcxyz', 'xyz') as trim;

     untrim      |   trim
-----------------+-----------
 xyzaxyzbxyzcxyz | axyzbxyzc
(1 row)
```

Note that the leading and trailing occurrences of `'xyz'` were removed, but that occurrences that were internal within the string were not removed\. 