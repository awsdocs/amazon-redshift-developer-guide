# NVL2 expression<a name="r_NVL2"></a>

Returns one of two values based on whether a specified expression evaluates to NULL or NOT NULL\.

## Syntax<a name="r_NVL2-synopsis"></a>

```
NVL2 ( expression, not_null_return_value, null_return_value )
```

## Arguments<a name="r_NVL2-arguments"></a>

 *expression*   
An expression, such as a column name, to be evaluated for null status\.

 *not\_null\_return\_value*   
The value returned if *expression* evaluates to NOT NULL\. The *not\_null\_return\_value* value must either have the same data type as *expression* or be implicitly convertible to that data type\.

 *null\_return\_value*   
The value returned if *expression* evaluates to NULL\. The *null\_return\_value* value must either have the same data type as *expression* or be implicitly convertible to that data type\.

## Return type<a name="r_NVL2-return-type"></a>

The NVL2 return type is determined as follows:
+ If either *not\_null\_return\_value* or *null\_return\_value* is null, the data type of the not\-null expression is returned\.

If both *not\_null\_return\_value* and *null\_return\_value* are not null:
+ If *not\_null\_return\_value* and *null\_return\_value* have the same data type, that data type is returned\.
+ If *not\_null\_return\_value* and *null\_return\_value* have different numeric data types, the smallest compatible numeric data type is returned\.
+ If *not\_null\_return\_value* and *null\_return\_value* have different datetime data types, a timestamp data type is returned\.
+ If *not\_null\_return\_value* and *null\_return\_value* have different character data types, the data type of *not\_null\_return\_value* is returned\.
+ If *not\_null\_return\_value* and *null\_return\_value* have mixed numeric and non\-numeric data types, the data type of *not\_null\_return\_value* is returned\.

**Important**  
In the last two cases where the data type of *not\_null\_return\_value* is returned, *null\_return\_value* is implicitly cast to that data type\. If the data types are incompatible, the function fails\.

## Usage notes<a name="nvl2-usage-notes"></a>

[DECODE expression](r_DECODE_expression.md) can be used in a similar way to NVL2 when the *expression* and *search* parameters are both null\. The difference is that for DECODE, the return will have both the value and the data type of the *result* parameter\. In contrast, for NVL2, the return will have the value of either the *not\_null\_return\_value* or *null\_return\_value* parameter, whichever is selected by the function, but will have the data type of *not\_null\_return\_value*\.

For example, assuming column1 is NULL, the following queries will return the same value\. However, the DECODE return value data type will be INTEGER and the NVL2 return value data type will be VARCHAR\.

```
select decode(column1, null, 1234, '2345');
select nvl2(column1, '2345', 1234);
```

## Example<a name="r_NVL2-examples"></a>

The following example modifies some sample data, then evaluates two fields to provide appropriate contact information for users: 

```
update users set email = null where firstname = 'Aphrodite' and lastname = 'Acevedo';

select (firstname + ' ' + lastname) as name, 
nvl2(email, email, phone) AS contact_info
from users 
where state = 'WA'
and lastname  like 'A%'
order by lastname, firstname;

name			     contact_info	
--------------------+-------------------------------------------
Aphrodite Acevedo	(906) 632-4407
Caldwell Acevedo 	Nunc.sollicitudin@Duisac.ca
Quinn Adams		  vel@adipiscingligulaAenean.com
Kamal Aguilar		quis@vulputaterisusa.com
Samson Alexander	 hendrerit.neque@indolorFusce.ca
Hall Alford		  ac.mattis@vitaediamProin.edu
Lane Allen		   et.netus@risusDonec.org
Xander Allison	   ac.facilisis.facilisis@Infaucibus.com
Amaya Alvarado	   dui.nec.tempus@eudui.edu
Vera Alvarez		 at.arcu.Vestibulum@pellentesque.edu
Yetta Anthony		enim.sit@risus.org
Violet Arnold		ad.litora@at.com
August Ashley		consectetuer.euismod@Phasellus.com
Karyn Austin		 ipsum.primis.in@Maurisblanditenim.org
Lucas Ayers		  at@elitpretiumet.com
```