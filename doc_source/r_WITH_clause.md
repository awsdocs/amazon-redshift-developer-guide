# WITH clause<a name="r_WITH_clause"></a>

A WITH clause is an optional clause that precedes the SELECT list in a query\. The WITH clause defines one or more *common\_table\_expressions*\. Each common table expression \(CTE\) defines a temporary table, which is similar to a view definition\. You can reference these temporary tables in the FROM clause\. They're used only while the query they belong to runs\. Each CTE in the WITH clause specifies a table name, an optional list of column names, and a query expression that evaluates to a table \(a SELECT statement\)\. When you reference the temporary table name in the FROM clause of the same query expression that defines it, the CTE is recursive\. 

WITH clause subqueries are an efficient way of defining tables that can be used throughout the execution of a single query\. In all cases, the same results can be achieved by using subqueries in the main body of the SELECT statement, but WITH clause subqueries may be simpler to write and read\. Where possible, WITH clause subqueries that are referenced multiple times are optimized as common subexpressions; that is, it may be possible to evaluate a WITH subquery once and reuse its results\. \(Note that common subexpressions aren't limited to those defined in the WITH clause\.\)

## Syntax<a name="r_WITH_clause-synopsis"></a>

```
[ WITH [RECURSIVE] common_table_expression [, common_table_expression , ...] ]
```

where *common\_table\_expression* can be either non\-recursive or recursive\. Following is the non\-recursive form: 

```
CTE_table_name [ ( column_name [, ...] ) ] AS ( query )
```

Following is the recursive form of *common\_table\_expression*:

```
CTE_table_name (column_name [, ...] ) AS ( recursive_query )
```

## Parameters<a name="r_WITH_clause-parameters"></a>

 RECURSIVE   
Keyword that identifies the query as a recursive CTE\. This keyword is required if any *common\_table\_expression* defined in the WITH clause is recursive\. You can only specify the RECURSIVE keyword once, immediately following the WITH keyword, even when the WITH clause contains multiple recursive CTEs\. In general, a recursive CTE is a UNION ALL subquery with two parts\. 

 *common\_table\_expression*   
Defines a temporary table that you can reference in the [FROM clause](r_FROM_clause30.md) and is used only during the execution of the query to which it belongs\. 

 *CTE\_table\_name*   
A unique name for a temporary table that defines the results of a WITH clause subquery\. You can't use duplicate names within a single WITH clause\. Each subquery must be given a table name that can be referenced in the [FROM clause](r_FROM_clause30.md)\.

 *column\_name*   
 A list of output column names for the WITH clause subquery, separated by commas\. The number of column names specified must be equal to or less than the number of columns defined by the subquery\. For a CTE that is non\-recursive, the *column\_name* clause is optional\. For a recursive CTE, the *column\_name* list is required\.

 *query*   
 Any SELECT query that Amazon Redshift supports\. See [SELECT](r_SELECT_synopsis.md)\. 

 *recursive\_query*   
A UNION ALL query that consists of two SELECT subqueries:  
+ The first SELECT subquery doesn't have a recursive reference to the same *CTE\_table\_name*\. It returns a result set that is the initial seed of the recursion\. This part is called the initial member or seed member\.
+ The second SELECT subquery references the same *CTE\_table\_name* in its FROM clause\. This is called the recursive member\. The *recursive\_query* contains a WHERE condition to end the *recursive\_query*\. 

## Usage notes<a name="r_WITH_clause-usage-notes"></a>

You can use a WITH clause in the following SQL statements: 
+ SELECT 
+ SELECT INTO
+ CREATE TABLE AS
+ CREATE VIEW
+ DECLARE
+ EXPLAIN
+ INSERT INTO\.\.\.SELECT 
+ PREPARE
+ UPDATE \(within a WHERE clause subquery\. You can't define a recursive CTE in the subquery\. The recursive CTE must precede the UPDATE clause\.\)
+ DELETE

If the FROM clause of a query that contains a WITH clause doesn't reference any of the tables defined by the WITH clause, the WITH clause is ignored and the query runs as normal\.

A table defined by a WITH clause subquery can be referenced only in the scope of the SELECT query that the WITH clause begins\. For example, you can reference such a table in the FROM clause of a subquery in the SELECT list, WHERE clause, or HAVING clause\. You can't use a WITH clause in a subquery and reference its table in the FROM clause of the main query or another subquery\. This query pattern results in an error message of the form `relation table_name doesn't exist` for the WITH clause table\.

You can't specify another WITH clause inside a WITH clause subquery\.

You can't make forward references to tables defined by WITH clause subqueries\. For example, the following query returns an error because of the forward reference to table W2 in the definition of table W1: 

```
with w1 as (select * from w2), w2 as (select * from w1)
select * from sales;
ERROR:  relation "w2" does not exist
```

A WITH clause subquery may not consist of a SELECT INTO statement; however, you can use a WITH clause in a SELECT INTO statement\.

## Recursive common table expressions<a name="r_WITH_clause-recursive-cte"></a>

A recursive *common table expression \(CTE\)* is a CTE that references itself\. A recursive CTE is useful in querying hierarchical data, such as organization charts that show reporting relationships between employees and managers\. See [Example: Recursive CTE](#r_WITH_clause-recursive-cte-example)\.

Another common use is a multilevel bill of materials, when a product consists of many components and each component itself also consists of other components or subassemblies\.

Be sure to limit the depth of recursion by including a WHERE clause in the second SELECT subquery of the recursive query\. For an example, see [Example: Recursive CTE](#r_WITH_clause-recursive-cte-example)\. Otherwise, an error can occur similar to the following:
+ `Recursive CTE out of working buffers.`
+ `Exceeded recursive CTE max rows limit.`

 You can specify a sort order and limit on the result of the recursive CTE\. You can include group by and distinct options on the final result of the recursive CTE\.

You can't specify a WITH RECURSIVE clause inside a subquery\. The *recursive\_query* member can't include an order by or limit clause\. 

## Examples<a name="r_WITH_clause-examples"></a>

The following example shows the simplest possible case of a query that contains a WITH clause\. The WITH query named VENUECOPY selects all of the rows from the VENUE table\. The main query in turn selects all of the rows from VENUECOPY\. The VENUECOPY table exists only for the duration of this query\. 

```
with venuecopy as (select * from venue)
select * from venuecopy order by 1 limit 10;
```

```
 venueid |         venuename          |    venuecity    | venuestate | venueseats
---------+----------------------------+-----------------+------------+------------
1 | Toyota Park                | Bridgeview      | IL         |          0
2 | Columbus Crew Stadium      | Columbus        | OH         |          0
3 | RFK Stadium                | Washington      | DC         |          0
4 | CommunityAmerica Ballpark  | Kansas City     | KS         |          0
5 | Gillette Stadium           | Foxborough      | MA         |      68756
6 | New York Giants Stadium    | East Rutherford | NJ         |      80242
7 | BMO Field                  | Toronto         | ON         |          0
8 | The Home Depot Center      | Carson          | CA         |          0
9 | Dick's Sporting Goods Park | Commerce City   | CO         |          0
v     10 | Pizza Hut Park             | Frisco          | TX         |          0
(10 rows)
```

The following example shows a WITH clause that produces two tables, named VENUE\_SALES and TOP\_VENUES\. The second WITH query table selects from the first\. In turn, the WHERE clause of the main query block contains a subquery that constrains the TOP\_VENUES table\. 

```
with venue_sales as
(select venuename, venuecity, sum(pricepaid) as venuename_sales
from sales, venue, event
where venue.venueid=event.venueid and event.eventid=sales.eventid
group by venuename, venuecity),

top_venues as
(select venuename
from venue_sales
where venuename_sales > 800000)

select venuename, venuecity, venuestate,
sum(qtysold) as venue_qty,
sum(pricepaid) as venue_sales
from sales, venue, event
where venue.venueid=event.venueid and event.eventid=sales.eventid
and venuename in(select venuename from top_venues)
group by venuename, venuecity, venuestate
order by venuename;
```

```
        venuename       |   venuecity   | venuestate | venue_qty | venue_sales
------------------------+---------------+------------+-----------+-------------
August Wilson Theatre   | New York City | NY         |      3187 |  1032156.00
Biltmore Theatre        | New York City | NY         |      2629 |   828981.00
Charles Playhouse       | Boston        | MA         |      2502 |   857031.00
Ethel Barrymore Theatre | New York City | NY         |      2828 |   891172.00
Eugene O'Neill Theatre  | New York City | NY         |      2488 |   828950.00
Greek Theatre           | Los Angeles   | CA         |      2445 |   838918.00
Helen Hayes Theatre     | New York City | NY         |      2948 |   978765.00
Hilton Theatre          | New York City | NY         |      2999 |   885686.00
Imperial Theatre        | New York City | NY         |      2702 |   877993.00
Lunt-Fontanne Theatre   | New York City | NY         |      3326 |  1115182.00
Majestic Theatre        | New York City | NY         |      2549 |   894275.00
Nederlander Theatre     | New York City | NY         |      2934 |   936312.00
Pasadena Playhouse      | Pasadena      | CA         |      2739 |   820435.00
Winter Garden Theatre   | New York City | NY         |      2838 |   939257.00
(14 rows)
```

The following two examples demonstrate the rules for the scope of table references based on WITH clause subqueries\. The first query runs, but the second fails with an expected error\. The first query has WITH clause subquery inside the SELECT list of the main query\. The table defined by the WITH clause \(HOLIDAYS\) is referenced in the FROM clause of the subquery in the SELECT list: 

```
select caldate, sum(pricepaid) as daysales,
(with holidays as (select * from date where holiday ='t')
select sum(pricepaid)
from sales join holidays on sales.dateid=holidays.dateid
where caldate='2008-12-25') as dec25sales
from sales join date on sales.dateid=date.dateid
where caldate in('2008-12-25','2008-12-31')
group by caldate
order by caldate;

caldate   | daysales | dec25sales
-----------+----------+------------
2008-12-25 | 70402.00 |   70402.00
2008-12-31 | 12678.00 |   70402.00
(2 rows)
```

The second query fails because it attempts to reference the HOLIDAYS table in the main query as well as in the SELECT list subquery\. The main query references are out of scope\. 

```
select caldate, sum(pricepaid) as daysales,
(with holidays as (select * from date where holiday ='t')
select sum(pricepaid)
from sales join holidays on sales.dateid=holidays.dateid
where caldate='2008-12-25') as dec25sales
from sales join holidays on sales.dateid=holidays.dateid
where caldate in('2008-12-25','2008-12-31')
group by caldate
order by caldate;

ERROR:  relation "holidays" does not exist
```

## Example: Recursive CTE<a name="r_WITH_clause-recursive-cte-example"></a>

The following is an example of a recursive CTE that returns the employees who report directly or indirectly to John\. The recursive query contains a WHERE clause to limit the depth of recursion to less than 4 levels\.

```
--create and populate the sample table
  create table employee (
  id int,
  name varchar (20),
  manager_id int
  );
  
  insert into employee(id, name, manager_id)  values
(100, 'Carlos', null),
(101, 'John', 100),
(102, 'Jorge', 101),
(103, 'Kwaku', 101),
(110, 'Liu', 101),
(106, 'Mateo', 102),
(110, 'Nikki', 103),
(104, 'Paulo', 103),
(105, 'Richard', 103),
(120, 'Saanvi', 104),
(200, 'Shirley', 104),
(201, 'Sofía', 102),
(205, 'Zhang', 104);
  
--run the recursive query
  with recursive john_org(id, name, manager_id, level) as
( select id, name, manager_id, 1 as level
  from employee
  where name = 'John'
  union all
  select e.id, e.name, e.manager_id, level + 1 as next_level
  from employee e, john_org j
  where e.manager_id = j.id and level < 4
  )
 select distinct id, name, manager_id from john_org order by manager_id;
```

Following is the result of the query\.

```
    id        name      manager_id
  ------+-----------+--------------
   101    John           100
   102    Jorge          101
   103    Kwaku          101
   110    Liu            101
   201    Sofía          102
   106    Mateo          102
   110    Nikki          103
   104    Paulo          103
   105    Richard        103
   120    Saanvi         104
   200    Shirley        104
   205    Zhang          104
```

Following is an organization chart for John's department\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/org-chart.png)