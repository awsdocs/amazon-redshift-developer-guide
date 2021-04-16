# Example UNION queries<a name="c_example_union_query"></a>

In the following UNION query, rows in the SALES table are merged with rows in the LISTING table\. Three compatible columns are selected from each table; in this case, the corresponding columns have the same names and data types\. 

The final result set is ordered by the first column in the LISTING table and limited to the 5 rows with the highest LISTID value\. 

```
select listid, sellerid, eventid from listing
union select listid, sellerid, eventid from sales
order by listid, sellerid, eventid desc limit 5;

listid | sellerid | eventid
--------+----------+---------
1 |    36861 |    7872
2 |    16002 |    4806
3 |    21461 |    4256
4 |     8117 |    4337
5 |     1616 |    8647
(5 rows)
```

The following example shows how you can add a literal value to the output of a UNION query so you can see which query expression produced each row in the result set\. The query identifies rows from the first query expression as "B" \(for buyers\) and rows from the second query expression as "S" \(for sellers\)\. 

The query identifies buyers and sellers for ticket transactions that cost $10,000 or more\. The only difference between the two query expressions on either side of the UNION operator is the joining column for the SALES table\. 

```
select listid, lastname, firstname, username,
pricepaid as price, 'S' as buyorsell
from sales, users
where sales.sellerid=users.userid
and pricepaid >=10000
union
select listid, lastname, firstname, username, pricepaid,
'B' as buyorsell
from sales, users
where sales.buyerid=users.userid
and pricepaid >=10000
order by 1, 2, 3, 4, 5;

listid | lastname | firstname | username |   price   | buyorsell
--------+----------+-----------+----------+-----------+-----------
209658 | Lamb     | Colette   | VOR15LYI |  10000.00 | B
209658 | West     | Kato      | ELU81XAA |  10000.00 | S
212395 | Greer    | Harlan    | GXO71KOC |  12624.00 | S
212395 | Perry    | Cora      | YWR73YNZ |  12624.00 | B
215156 | Banks    | Patrick   | ZNQ69CLT |  10000.00 | S
215156 | Hayden   | Malachi   | BBG56AKU |  10000.00 | B
(6 rows)
```

The following example uses a UNION ALL operator because duplicate rows, if found, need to be retained in the result\. For a specific series of event IDs, the query returns 0 or more rows for each sale associated with each event, and 0 or 1 row for each listing of that event\. Event IDs are unique to each row in the LISTING and EVENT tables, but there might be multiple sales for the same combination of event and listing IDs in the SALES table\. 

The third column in the result set identifies the source of the row\. If it comes from the SALES table, it is marked "Yes" in the SALESROW column\. \(SALESROW is an alias for SALES\.LISTID\.\) If the row comes from the LISTING table, it is marked "No" in the SALESROW column\. 

In this case, the result set consists of three sales rows for listing 500, event 7787\. In other words, three different transactions took place for this listing and event combination\. The other two listings, 501 and 502, did not produce any sales, so the only row that the query produces for these list IDs comes from the LISTING table \(SALESROW = 'No'\)\. 

```
select eventid, listid, 'Yes' as salesrow
from sales
where listid in(500,501,502)
union all
select eventid, listid, 'No'
from listing
where listid in(500,501,502)
order by listid asc;

eventid | listid | salesrow
---------+--------+----------
7787 |    500 | No
7787 |    500 | Yes
7787 |    500 | Yes
7787 |    500 | Yes
6473 |    501 | No
5108 |    502 | No
(6 rows)
```

If you run the same query without the ALL keyword, the result retains only one of the sales transactions\. 

```
select eventid, listid, 'Yes' as salesrow
from sales
where listid in(500,501,502)
union
select eventid, listid, 'No'
from listing
where listid in(500,501,502)
order by listid asc;

eventid | listid | salesrow
---------+--------+----------
7787 |    500 | No
7787 |    500 | Yes
6473 |    501 | No
5108 |    502 | No
(4 rows)
```