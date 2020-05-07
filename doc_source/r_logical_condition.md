# Logical conditions<a name="r_logical_condition"></a>

Logical conditions combine the result of two conditions to produce a single result\. All logical conditions are binary operators with a Boolean return type\. 

## Syntax<a name="r_logical_condition-synopsis"></a>

```
expression
{ AND | OR }
expression
NOT expression
```

Logical conditions use a three\-valued Boolean logic where the null value represents an unknown relationship\. The following table describes the results for logical conditions, where `E1` and `E2` represent expressions:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_logical_condition.html)

The NOT operator is evaluated before AND, and the AND operator is evaluated before the OR operator\. Any parentheses used may override this default order of evaluation\. 

### Examples<a name="r_logical_condition-examples"></a>

The following example returns USERID and USERNAME from the USERS table where the user likes both Las Vegas and sports: 

```
select userid, username from users
where likevegas = 1 and likesports = 1
order by userid;

userid | username
--------+----------
1 | JSG99FHE
67 | TWU10MZT
87 | DUF19VXU
92 | HYP36WEQ
109 | FPL38HZK
120 | DMJ24GUZ
123 | QZR22XGQ
130 | ZQC82ALK
133 | LBN45WCH
144 | UCX04JKN
165 | TEY68OEB
169 | AYQ83HGO
184 | TVX65AZX
...
(2128 rows)
```

The next example returns the USERID and USERNAME from the USERS table where the user likes Las Vegas, or sports, or both\. This query returns all of the output from the previous example plus the users who like only Las Vegas or sports\. 

```
select userid, username from users
where likevegas = 1 or likesports = 1
order by userid;

userid | username
--------+----------
1 | JSG99FHE
2 | PGL08LJI
3 | IFT66TXU
5 | AEB55QTM
6 | NDQ15VBM
9 | MSD36KVR
10 | WKW41AIW
13 | QTF33MCG
15 | OWU78MTR
16 | ZMG93CDD
22 | RHT62AGI
27 | KOY02CVE
29 | HUH27PKK
...
(18968 rows)
```

The following query uses parentheses around the `OR` condition to find venues in New York or California where Macbeth was performed: 

```
select distinct venuename, venuecity
from venue join event on venue.venueid=event.venueid
where (venuestate = 'NY' or venuestate = 'CA') and eventname='Macbeth'
order by 2,1;

venuename                |   venuecity
----------------------------------------+---------------
Geffen Playhouse                       | Los Angeles
Greek Theatre                          | Los Angeles
Royce Hall                             | Los Angeles
American Airlines Theatre              | New York City
August Wilson Theatre                  | New York City
Belasco Theatre                        | New York City
Bernard B. Jacobs Theatre              | New York City
...
```

Removing the parentheses in this example changes the logic and results of the query\. 

The following example uses the `NOT` operator: 

```
select * from category
where not catid=1
order by 1;

catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+--------------------------------------------
2 | Sports   | NHL       | National Hockey League
3 | Sports   | NFL       | National Football League
4 | Sports   | NBA       | National Basketball Association
5 | Sports   | MLS       | Major League Soccer
...
```

The following example uses a `NOT` condition followed by an `AND` condition: 

```
select * from category
where (not catid=1) and catgroup='Sports'
order by catid;

catid | catgroup | catname |             catdesc
-------+----------+---------+---------------------------------
2 | Sports   | NHL     | National Hockey League
3 | Sports   | NFL     | National Football League
4 | Sports   | NBA     | National Basketball Association
5 | Sports   | MLS     | Major League Soccer
(4 rows)
```