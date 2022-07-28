# split\_to\_array function<a name="split_to_array"></a>

Uses a delimiter as an optional parameter\. If no delimiter is present, then the default is a comma\.

## Syntax<a name="split_to_array-syntax"></a>

```
split_to_array( string,delimiter )
```

## Arguments<a name="split_to_array-arguments"></a>

 **string**   
The input string to be split\.

 **delimiter**   
An optional value on which the input string will be split\. The default is a comma\.

## Return type<a name="split_to_array-return-type"></a>

The split\_to\_array function returns a SUPER data value\.

## Example<a name="split_to_array-example"></a>

The following example show a split\_to\_array function\.

```
SELECT SPLIT_TO_ARRAY('12|345|6789', '|');
     split_to_array
-------------------------
 ["12","345","6789"]
(1 row)
```