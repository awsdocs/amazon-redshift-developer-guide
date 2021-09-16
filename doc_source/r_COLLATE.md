# COLLATE function<a name="r_COLLATE"></a>

The COLLATE function overrides the collation of a string column or expression\. 

For information on how to create tables using database collation, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

For information on how to create databases using database collation, see [CREATE DATABASE](r_CREATE_DATABASE.md)\.

## Syntax<a name="r_COLLATE-synopsis"></a>

```
COLLATE(string, 'case_sensitive' | 'case_insensitive');
```

## Argument<a name="r_COLLATE-argument"></a>

 *string*   
The COLLATE function takes two input arguments\. The first input is a string column or expression that you want to override\. The second input is a string constant of a collation name\. Amazon Redshift only supports *case\_sensitive* or *case\_insensitive*\.

## Return type<a name="r_COLLATE-return-type"></a>

The COLLATE function returns VARCHAR or CHAR depending on the first input expression type\. This function only changes the collation of the first input argument and won't change its output value\.

## Example<a name="r_COLLATE-example"></a>

The following example defines col1 in table T1 as case\_sensitive\. When you run the first query, Amazon Redshift only returns one row ‘john’\. After collate function runs on col1, the collation becomes case\_insensitive\. The second query returns both ‘john’ and ‘JOHN’\.

```
CREATE TABLE T (
  col1 Varchar(20) COLLATE case_sensitive
 );
```

```
INSERT INTO T VALUES ('john'),('JOHN');
```

```
SELECT * FROM T WHERE col1 = 'john';
 col1
------
 john
(1 rows)
```

```
SELECT * FROM T WHERE collate(col1, 'case_insensitive') = 'john';
 col1
------
 john
 JOHN
(2 rows)
```