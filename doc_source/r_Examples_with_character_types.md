# Examples with character types<a name="r_Examples_with_character_types"></a>

## CREATE TABLE statement<a name="r_Examples_with_character_types-create-table-statement"></a>

The following CREATE TABLE statement demonstrates the use of VARCHAR and CHAR data types: 

```
create table address(
address_id integer,
address1 varchar(100),
address2 varchar(50),
district varchar(20),
city_name char(20),
state char(2),
postal_code char(5)
);
```

The following examples use this table\. 

## Trailing blanks in variable\-length character strings<a name="r_Examples_with_character_types-trailing-blanks-in-variable-length-character-strings"></a>

Because ADDRESS1 is a VARCHAR column, the trailing blanks in the second inserted address are semantically insignificant\. In other words, these two inserted addresses *match*\. 

```
insert into address(address1) values('9516 Magnolia Boulevard');

insert into address(address1) values('9516 Magnolia Boulevard  ');
```

```
select count(*) from address
where address1='9516 Magnolia Boulevard';

count
-------
2
(1 row)
```

If the ADDRESS1 column were a CHAR column and the same values were inserted, the COUNT\(\*\) query would recognize the character strings as the same and return `2`\.

## Results of the LENGTH function<a name="r_Examples_with_character_types-results-of-the-length-function"></a>

The LENGTH function recognizes trailing blanks in VARCHAR columns: 

```
select length(address1) from address;

length
--------
23
25
(2 rows)
```

A value of `Augusta` in the CITY\_NAME column, which is a CHAR column, would always return a length of 7 characters, regardless of any trailing blanks in the input string\. 

## Values that exceed the length of the column<a name="r_Examples_with_character_types-values-that-exceed-the-length-of-the-column"></a>

Character strings are not truncated to fit the declared width of the column: 

```
insert into address(city_name) values('City of South San Francisco');
ERROR: value too long for type character(20)
```

A workaround for this problem is to cast the value to the size of the column: 

```
insert into address(city_name)
values('City of South San Francisco'::char(20));
```

In this case, the first 20 characters of the string \(`City of South San Fr`\) would be loaded into the column\. 