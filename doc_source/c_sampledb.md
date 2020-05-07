# Sample database<a name="c_sampledb"></a>

**Topics**
+ [CATEGORY table](r_categorytable.md)
+ [DATE table](r_datetable.md)
+ [EVENT table](r_eventtable.md)
+ [VENUE table](r_venuetable.md)
+ [USERS table](r_userstable.md)
+ [LISTING table](r_listingtable.md)
+ [SALES table](r_salestable.md)

Most of the examples in the Amazon Redshift documentation use a sample database called TICKIT\. This small database consists of seven tables: two fact tables and five dimensions\. You can load the TICKIT dataset by following the steps in [Step 6: Load sample data from amazon S3](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-create-sample-db.html) in the Amazon Redshift Getting Started\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tickitdb.png)

This sample database application helps analysts track sales activity for the fictional TICKIT web site, where users buy and sell tickets online for sporting events, shows, and concerts\. In particular, analysts can identify ticket movement over time, success rates for sellers, and the best\-selling events, venues, and seasons\. Analysts can use this information to provide incentives to buyers and sellers who frequent the site, to attract new users, and to drive advertising and promotions\.

For example, the following query finds the top five sellers in San Diego, based on the number of tickets sold in 2008: 

```
select sellerid, username, (firstname ||' '|| lastname) as name,
city, sum(qtysold)
from sales, date, users
where sales.sellerid = users.userid
and sales.dateid = date.dateid
and year = 2008
and city = 'San Diego'
group by sellerid, username, name, city
order by 5 desc
limit 5;

sellerid | username |       name        |   city    | sum
----------+----------+-------------------+-----------+-----
49977 | JJK84WTE | Julie Hanson      | San Diego |  22
19750 | AAS23BDR | Charity Zimmerman | San Diego |  21
29069 | SVL81MEQ | Axel Grant        | San Diego |  17
43632 | VAG08HKW | Griffin Dodson    | San Diego |  16
36712 | RXT40MKU | Hiram Turner      | San Diego |  14
(5 rows)
```

The database used for the examples in this guide contains a small data set; the two fact tables each contain less than 200,000 rows, and the dimensions range from 11 rows in the CATEGORY table up to about 50,000 rows in the USERS table\.

In particular, the database examples in this guide demonstrate the key features of Amazon Redshift table design: 
+ Data distribution
+ Data sort
+ Columnar compression