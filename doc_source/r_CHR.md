# CHR function<a name="r_CHR"></a>

The CHR function returns the character that matches the ASCII code point value specified by of the input parameter\.

## Syntax<a name="r_CHR-synopsis"></a>

```
CHR(number)
```

## Argument<a name="r_CHR-argument"></a>

 *number*   
The input parameter is an integer that represents an ASCII code point value\.

## Return type<a name="r_CHR-return-type"></a>

The CHR function returns a CHAR string if an ASCII character matches the input value\. If the input number has no ASCII match, the function returns null\.

## Example<a name="r_CHR-example"></a>

The following example returns event names that begin with a capital A \(ASCII code point 65\): 

```
select distinct eventname from event
where substring(eventname, 1, 1)=chr(65);

eventname
---------------------------
Adriana Lecouvreur
A Man For All Seasons
A Bronx Tale
A Christmas Carol
Allman Brothers Band
...
```