# GREATEST and LEAST<a name="r_GREATEST_LEAST"></a>

Returns the largest or smallest value from a list of any number of expressions\.

## Syntax<a name="r_GREATEST_LEAST-synopsis"></a>

```
GREATEST (value [, ...])
LEAST (value [, ...])
```

## Parameters<a name="r_GREATEST_LEAST-arguments"></a>

*expression\_list*  
A comma\-separated list of expressions, such as column names\. The expressions must all be convertible to a common data type\. NULL values in the list are ignored\. If all of the expressions evaluate to NULL, the result is NULL\.

## Returns<a name="r_GREATEST_LEAST-returns"></a>

Returns the data type of the expressions\.

## Example<a name="r_GREATEST_LEAST-examples"></a>

The following example returns the highest value alphabetically for `firstname` or `lastname`\.

```
select firstname, lastname, greatest(firstname,lastname) from users
where userid < 10
order by 3;

 firstname | lastname  | greatest
-----------+-----------+-----------
 Lars      | Ratliff   | Ratliff
 Reagan    | Hodge     | Reagan
 Colton    | Roy       | Roy
 Barry     | Roy       | Roy
 Tamekah   | Juarez    | Tamekah
 Rafael    | Taylor    | Taylor
 Victor    | Hernandez | Victor
 Vladimir  | Humphrey  | Vladimir
 Mufutau   | Watkins   | Watkins
(9 rows)
```