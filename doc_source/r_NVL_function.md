# NVL function<a name="r_NVL_function"></a>

Returns the value of the first expression that isn't null in a series of expressions\. When a non\-null value is found, the remaining expressions in the list aren't evaluated\. 

NVL is identical to COALESCE\. They are synonyms\. This topic explains the syntax and contains examples for both\.

## Syntax<a name="r_NVL_function-synopsis"></a>

```
NVL ( expression, expression, ... )
```

The syntax for COALESCE is the same:

```
COALESCE ( expression, expression, ... )
```

If all expressions are null, the result is null\.

These functions are useful when you want to return a secondary value when a primary value is missing or null\. For example, a query might return the first of three available phone numbers: cell, home, or work\. The order of the expressions in the function determines the order of evaluation\.

## Arguments<a name="r_NVL_function-arguments"></a>

 *expression*   
An expression, such as a column name, to be evaluated for null status\.

## Return type<a name="r_NVL_function-returntype"></a>

Amazon Redshift determines the data type of the returned value based on the input expressions\. If the data types of the input expressions don't have a common type, then an error is returned\.

## Examples<a name="r_NVL_function-examples"></a>

If the list contains integer expressions, the function returns an integer\. 

```
SELECT COALESCE(NULL, 12, NULL);

coalesce
--------------
12
```

The following example returns a string type\.

```
SELECT COALESCE(NULL, 'Amazon Redshift', NULL);

coalesce
--------------
Amazon Redshift
```

The following example results in an error because the data types vary in the expression list\. In this case, there is both a string type and a number type in the list\.

```
SELECT COALESCE(NULL, 'Amazon Redshift', 12);

ERROR: invalid input syntax for integer: "Amazon Redshift"
```

For this example, you create a table with START\_DATE and END\_DATE columns, insert rows that include null values, then apply an NVL expression to the two columns\.

```
create table datetable (start_date date, end_date date);
               
insert into datetable values ('2008-06-01','2008-12-31');
insert into datetable values (null,'2008-12-31');
insert into datetable values ('2008-12-31',null);
```

```
select nvl(start_date, end_date)
from datetable
order by 1;
               
coalesce
------------
2008-06-01
2008-12-31
2008-12-31
```

The default column name for an NVL expression is COALESCE\. The following query returns the same results:

```
select coalesce(start_date, end_date)
from datetable
order by 1;
```

If you expect a query to return null values for certain functions or columns, you can use an NVL expression to replace the nulls with some other value\. For example, aggregate functions, such as SUM, return null values instead of zeroes when they have no rows to evaluate\. You can use an NVL expression to replace these null values with `0.0`:

```
select nvl(sum(sales), 0.0) as sumresult, ...
```

For the following example queries, you create a table with sample hotel\-booking information and insert several rows\. Some records contain null values\.

```
create table booking_info (booking_id int, booking_code character(8), check_in date, check_out date, funds_collected numeric(12,2));
```

Insert the following sample data\. Some records don't have a `check_out` date\.

```
insert into booking_info values (1, 'OCEAN_WV', '2023-02-01','2023-02-03',100.00);
insert into booking_info values (2, 'OCEAN_WV', '2023-04-22','2023-04-26',120.00);
insert into booking_info values (3, 'DSRT_SUN', '2023-03-13','2023-03-16',125.00);
insert into booking_info values (4, 'DSRT_SUN', '2023-06-01','2023-06-03',140.00);
insert into booking_info values (5, 'DSRT_SUN', '2023-07-10',null,null);
insert into booking_info values (6, 'OCEAN_WV', '2023-08-15',null,null);
```

The following query returns a list of dates\. If the `check_out` date isn't available, it lists the `check_in` date\.

```
select coalesce(check_out, check_in)
from booking_info
order by check_out;
```

The results are the following\. Note that the last two records show the `check_in` date\.

```
coalesce
------------
2023-02-03	
2023-03-16	
2023-04-26	
2023-06-03	
2023-07-10	
2023-08-15
```