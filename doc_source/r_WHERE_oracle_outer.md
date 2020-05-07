# Oracle\-Style outer joins in the WHERE clause<a name="r_WHERE_oracle_outer"></a>

For Oracle compatibility, Amazon Redshift supports the Oracle outer\-join operator \(\+\) in WHERE clause join conditions\. This operator is intended for use only in defining outer\-join conditions; don't try to use it in other contexts\. Other uses of this operator are silently ignored in most cases\. 

An outer join returns all of the rows that the equivalent inner join would return, plus non\-matching rows from one or both tables\. In the FROM clause, you can specify left, right, and full outer joins\. In the WHERE clause, you can specify left and right outer joins only\. 

To outer join tables TABLE1 and TABLE2 and return non\-matching rows from TABLE1 \(a left outer join\), specify `TABLE1 LEFT OUTER JOIN TABLE2` in the FROM clause or apply the \(\+\) operator to all joining columns from TABLE2 in the WHERE clause\. For all rows in TABLE1 that have no matching rows in TABLE2, the result of the query contains nulls for any select list expressions that contain columns from TABLE2\. 

To produce the same behavior for all rows in TABLE2 that have no matching rows in TABLE1, specify `TABLE1 RIGHT OUTER JOIN TABLE2` in the FROM clause or apply the \(\+\) operator to all joining columns from TABLE1 in the WHERE clause\. 

## Basic syntax<a name="r_WHERE_oracle_outer-basic-syntax"></a>

```
[ WHERE {
[ table1.column1 = table2.column1(+) ]
[ table1.column1(+) = table2.column1 ]
}
```

The first condition is equivalent to: 

```
from table1 left outer join table2
on table1.column1=table2.column1
```

The second condition is equivalent to: 

```
from table1 right outer join table2
on table1.column1=table2.column1
```

**Note**  
The syntax shown here covers the simple case of an equijoin over one pair of joining columns\. However, other types of comparison conditions and multiple pairs of joining columns are also valid\. 

For example, the following WHERE clause defines an outer join over two pairs of columns\. The \(\+\) operator must be attached to the same table in both conditions: 

```
where table1.col1 > table2.col1(+)
and table1.col2 = table2.col2(+)
```

## Usage notes<a name="r_WHERE_oracle_outer_usage_notes"></a>

Where possible, use the standard FROM clause OUTER JOIN syntax instead of the \(\+\) operator in the WHERE clause\. Queries that contain the \(\+\) operator are subject to the following rules: 
+ You can only use the \(\+\) operator in the WHERE clause, and only in reference to columns from tables or views\. 
+ You can't apply the \(\+\) operator to expressions\. However, an expression can contain columns that use the \(\+\) operator\. For example, the following join condition returns a syntax error: 

  ```
  event.eventid*10(+)=category.catid
  ```

  However, the following join condition is valid: 

  ```
  event.eventid(+)*10=category.catid
  ```
+ You can't use the \(\+\) operator in a query block that also contains FROM clause join syntax\. 
+ If two tables are joined over multiple join conditions, you must use the \(\+\) operator in all or none of these conditions\. A join with mixed syntax styles executes as an inner join, without warning\. 
+ The \(\+\) operator doesn't produce an outer join if you join a table in the outer query with a table that results from an inner query\. 
+ To use the \(\+\) operator to outer\-join a table to itself, you must define table aliases in the FROM clause and reference them in the join condition: 

  ```
  select count(*)
  from event a, event b
  where a.eventid(+)=b.catid;
  
  count
  -------
  8798
  (1 row)
  ```
+ You can't combine a join condition that contains the \(\+\) operator with an OR condition or an IN condition\. For example: 

  ```
  select count(*) from sales, listing
  where sales.listid(+)=listing.listid or sales.salesid=0;
  ERROR:  Outer join operator (+) not allowed in operand of OR or IN.
  ```
+  In a WHERE clause that outer\-joins more than two tables, the \(\+\) operator can be applied only once to a given table\. In the following example, the SALES table can't be referenced with the \(\+\) operator in two successive joins\. 

  ```
  select count(*) from sales, listing, event
  where sales.listid(+)=listing.listid and sales.dateid(+)=date.dateid;
  ERROR:  A table may be outer joined to at most one other table.
  ```
+  If the WHERE clause outer\-join condition compares a column from TABLE2 with a constant, apply the \(\+\) operator to the column\. If you don't include the operator, the outer\-joined rows from TABLE1, which contain nulls for the restricted column, are eliminated\. See the Examples section below\. 

## Examples<a name="r_WHERE_oracle_outer-examples"></a>

The following join query specifies a left outer join of the SALES and LISTING tables over their LISTID columns: 

```
select count(*)
from sales, listing
where sales.listid = listing.listid(+);

count
--------
172456
(1 row)
```

The following equivalent query produces the same result but uses FROM clause join syntax: 

```
select count(*)
from sales left outer join listing on sales.listid = listing.listid;

count
--------
172456
(1 row)
```

The SALES table doesn't contain records for all listings in the LISTING table because not all listings result in sales\. The following query outer\-joins SALES and LISTING and returns rows from LISTING even when the SALES table reports no sales for a given list ID\. The PRICE and COMM columns, derived from the SALES table, contain nulls in the result set for those non\-matching rows\. 

```
select listing.listid, sum(pricepaid) as price,
sum(commission) as comm
from listing, sales
where sales.listid(+) = listing.listid and listing.listid between 1 and 5
group by 1 order by 1;

listid | price  |  comm
--------+--------+--------
1 | 728.00 | 109.20
2 |        |
3 |        |
4 |  76.00 |  11.40
5 | 525.00 |  78.75
(5 rows)
```

Note that when the WHERE clause join operator is used, the order of the tables in the FROM clause doesn't matter\. 

An example of a more complex outer join condition in the WHERE clause is the case where the condition consists of a comparison between two table columns *and* a comparison with a constant: 

```
where category.catid=event.catid(+) and eventid(+)=796;
```

Note that the \(\+\) operator is used in two places: first in the equality comparison between the tables and second in the comparison condition for the EVENTID column\. The result of this syntax is the preservation of the outer\-joined rows when the restriction on EVENTID is evaluated\. If you remove the \(\+\) operator from the EVENTID restriction, the query treats this restriction as a filter, not as part of the outer\-join condition\. In turn, the outer\-joined rows that contain nulls for EVENTID are eliminated from the result set\. 

Here is a complete query that illustrates this behavior: 

```
select catname, catgroup, eventid
from category, event
where category.catid=event.catid(+) and eventid(+)=796;

catname | catgroup | eventid
-----------+----------+---------
Classical | Concerts |
Jazz | Concerts |
MLB | Sports   |
MLS | Sports   |
Musicals | Shows    | 796
NBA | Sports   |
NFL | Sports   |
NHL | Sports   |
Opera | Shows    |
Plays | Shows    |
Pop | Concerts |
(11 rows)
```

The equivalent query using FROM clause syntax is as follows: 

```
select catname, catgroup, eventid
from category left join event
on category.catid=event.catid and eventid=796;
```

If you remove the second \(\+\) operator from the WHERE clause version of this query, it returns only 1 row \(the row where `eventid=796`\)\. 

```
select catname, catgroup, eventid
from category, event
where category.catid=event.catid(+) and eventid=796;

catname | catgroup | eventid
-----------+----------+---------
Musicals | Shows    | 796
(1 row)
```