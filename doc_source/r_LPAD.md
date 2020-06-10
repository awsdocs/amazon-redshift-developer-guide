# LPAD and RPAD functions<a name="r_LPAD"></a>

These functions prepend or append characters to a string, based on a specified length\. 

## Syntax<a name="r_LPAD-synopsis"></a>

```
LPAD (string1, length, [ string2 ])
```

```
RPAD (string1, length, [ string2 ])
```

## Arguments<a name="r_LPAD-arguments"></a>

 *string1*   
A character string or an expression that evaluates to a character string, such as the name of a character column\. 

 *length*   
An integer that defines the length of the result of the function\. The length of a string is based on the number of characters, not bytes, so that multi\-byte characters are counted as single characters\. If *string1* is longer than the specified length, it is truncated \(on the right\)\. If *length* is a negative number, the result of the function is an empty string\.

 *string2*   
One or more characters that are prepended or appended to *string1*\. This argument is optional; if it is not specified, spaces are used\. 

## Return type<a name="r_LPAD-return-type"></a>

These functions return a VARCHAR data type\. 

## Examples<a name="r_LPAD-examples"></a>

Truncate a specified set of event names to 20 characters and prepend the shorter names with spaces: 

```
select lpad(eventname,20) from event
where eventid between 1 and 5 order by 1;

 lpad
--------------------
              Salome
        Il Trovatore
       Boris Godunov
     Gotterdammerung
La Cenerentola (Cind
(5 rows)
```

Truncate the same set of event names to 20 characters but append the shorter names with `0123456789`\. 

```
select rpad(eventname,20,'0123456789') from event
where eventid between 1 and 5 order by 1;

 rpad
--------------------
Boris Godunov0123456
Gotterdammerung01234
Il Trovatore01234567
La Cenerentola (Cind
Salome01234567890123
(5 rows)
```