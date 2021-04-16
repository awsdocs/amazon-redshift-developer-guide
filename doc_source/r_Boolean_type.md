# Boolean type<a name="r_Boolean_type"></a>

Use the BOOLEAN data type to store true and false values in a single\-byte column\. The following table describes the three possible states for a Boolean value and the literal values that result in that state\. Regardless of the input string, a Boolean column stores and outputs "t" for true and "f" for false\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Boolean_type.html)

You can use an IS comparison to check a Boolean value only as a predicate in the WHERE clause\. You can't use the IS comparison with a Boolean value in the SELECT list\.

**Note**  
We recommend always checking Boolean values explicitly, as shown in the examples following\. Implicit comparisons, such as `WHERE flag` or `WHERE NOT flag` might return unexpected results\. 

## Examples<a name="r_Boolean_type-examples"></a>

You could use a BOOLEAN column to store an "Active/Inactive" state for each customer in a CUSTOMER table\.

```
create table customer(
custid int,
active_flag boolean default true);
```

```
insert into customer values(100, default);
```

```
select * from customer;
custid | active_flag
-------+--------------
   100 | t
```

If no default value \(`true` or `false`\) is specified in the CREATE TABLE statement, inserting a default value means inserting a null\. 

In this example, the query selects users from the USERS table who like sports but do not like theatre: 

```
select firstname, lastname, likesports, liketheatre
from users
where likesports is true and liketheatre is false
order by userid limit 10;

firstname |  lastname  | likesports | liketheatre
----------+------------+------------+-------------
Lars      | Ratliff    | t          | f
Mufutau   | Watkins    | t          | f
Scarlett  | Mayer      | t          | f
Shafira   | Glenn      | t          | f
Winifred  | Cherry     | t          | f
Chase     | Lamb       | t          | f
Liberty   | Ellison    | t          | f
Aladdin   | Haney      | t          | f
Tashya    | Michael    | t          | f
Lucian    | Montgomery | t          | f
(10 rows)
```

The following example selects users from the USERS table for whom is it unknown whether they like rock music\.

```
select firstname, lastname, likerock
from users
where likerock is unknown
order by userid limit 10;

firstname | lastname | likerock
----------+----------+----------
Rafael    | Taylor   |
Vladimir  | Humphrey |
Barry     | Roy      |
Tamekah   | Juarez   |
Mufutau   | Watkins  |
Naida     | Calderon |
Anika     | Huff     |
Bruce     | Beck     |
Mallory   | Farrell  |
Scarlett  | Mayer    |
(10 rows)
```

The following example returns an error because it uses an IS comparison in the SELECT list\.

```
select firstname, lastname, likerock is true as "check"
from users
order by userid limit 10;

[Amazon](500310) Invalid operation: Not implemented
```

The following example succeeds because it uses an equal comparison \( = \) in the SELECT list instead of the IS comparison\.

```
select firstname, lastname, likerock = true as "check"
from users
order by userid limit 10;

firstname | lastname  | check
----------+-----------+------
Rafael    | Taylor    |      
Vladimir  | Humphrey  |      
Lars      | Ratliff   | true 
Barry     | Roy       |      
Reagan    | Hodge     | true 
Victor    | Hernandez | true 
Tamekah   | Juarez    |      
Colton    | Roy       | false
Mufutau   | Watkins   |      
Naida     | Calderon  |
```