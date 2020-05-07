# Examples with numeric types<a name="r_Examples_with_numeric_types201"></a>

## CREATE TABLE statement<a name="r_Examples_with_numeric_types201-create-table-statement"></a>

The following CREATE TABLE statement demonstrates the declaration of different numeric data types:

```
create table film (
film_id integer,
language_id smallint,
original_language_id smallint,
rental_duration smallint default 3,
rental_rate numeric(4,2) default 4.99,
length smallint,
replacement_cost real default 25.00);
```

## Attempt to insert an integer that is out of range<a name="r_Examples_with_numeric_types201-attempt-to-insert-an-integer-that-is-out-of-range"></a>

The following example attempts to insert the value 33000 into a SMALLINT column\.

```
insert into film(language_id) values(33000);
```

The range for SMALLINT is \-32768 to \+32767, so Amazon Redshift returns an error\.

```
An error occurred when executing the SQL command:
insert into film(language_id) values(33000)

ERROR: smallint out of range [SQL State=22003]
```

## Insert a decimal value into an integer column<a name="r_Examples_with_numeric_types201-insert-a-decimal-value-into-an-integer-column"></a>

The following example inserts the a decimal value into an INT column\.

```
insert into film(language_id) values(1.5);
```

This value is inserted but rounded up to the integer value 2\.

## Insert a decimal that succeeds because its scale is rounded<a name="r_Examples_with_numeric_types201-insert-a-decimal-that-succeeds-because-its-scale-is-rounded"></a>

The following example inserts a decimal value that has higher precision that the column\.

```
insert into film(rental_rate) values(35.512);
```

In this case, the value `35.51` is inserted into the column\.

## Attempt to insert a decimal value that is out of range<a name="r_Examples_with_numeric_types201-attempt-to-insert-a-decimal-value-that-is-out-of-range"></a>

In this case, the value `350.10` is out of range\. The number of digits for values in DECIMAL columns is equal to the column's precision minus its scale \(4 minus 2 for the RENTAL\_RATE column\)\. In other words, the allowed range for a `DECIMAL(4,2)` column is `-99.99` through `99.99`\.

```
insert into film(rental_rate) values (350.10);
ERROR:  numeric field overflow
DETAIL:  The absolute value is greater than or equal to 10^2 for field with precision 4, scale 2.
```

## Insert variable\-precision values into a REAL column<a name="r_Examples_with_numeric_types201-insert-variable-precision-values-into-a-real-column"></a>

The following example inserts variable\-precision values into a REAL column\.

```
insert into film(replacement_cost) values(1999.99);

insert into film(replacement_cost) values(19999.99);

select replacement_cost from film;
replacement_cost
------------------
20000
1999.99
...
```

The value `19999.99` is converted to `20000` to meet the 6\-digit precision requirement for the column\. The value `1999.99` is loaded as is\.