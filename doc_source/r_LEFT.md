# LEFT and RIGHT functions<a name="r_LEFT"></a>

These functions return the specified number of leftmost or rightmost characters from a character string\.

The number is based on the number of characters, not bytes, so that multibyte characters are counted as single characters\.

## Syntax<a name="r_LEFT-synopsis"></a>

```
LEFT ( string,  integer )

RIGHT ( string,  integer )
```

## Arguments<a name="r_LEFT-arguments"></a>

 *string*   
Any character string or any expression that evaluates to a character string\. 

 *integer*   
A positive integer\. 

## Return type<a name="r_LEFT-return-type"></a>

LEFT and RIGHT return a VARCHAR string\. 

## Example<a name="r_LEFT-example"></a>

The following example returns the leftmost 5 and rightmost 5 characters from event names that have IDs between 1000 and 1005: 

```
select eventid, eventname,
left(eventname,5) as left_5,
right(eventname,5) as right_5
from event
where eventid between 1000 and 1005
order by 1;

eventid |   eventname    | left_5 | right_5
--------+----------------+--------+---------
   1000 | Gypsy          | Gypsy  | Gypsy
   1001 | Chicago        | Chica  | icago
   1002 | The King and I | The K  | and I
   1003 | Pal Joey       | Pal J  |  Joey
   1004 | Grease         | Greas  | rease
   1005 | Chicago        | Chica  | icago
(6 rows)
```