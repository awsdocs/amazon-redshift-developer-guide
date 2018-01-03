# LISTAGG Window Function Examples<a name="r_Examples_of_LISTAGG_WF"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\. 

The following example returns a list of seller IDs, ordered by seller ID\. 

```
select listagg(sellerid) 
within group (order by sellerid)
over() from winsales;

  listagg
------------
 11122333344
...
...
 11122333344
 11122333344
   (11 rows)
```

The following example returns a list of seller IDs for buyer B, ordered by date\. 

```
select listagg(sellerid) 
within group (order by dateid)
over () as seller
from winsales
where buyerid = 'b' ;

  seller
---------
    3233
    3233
    3233
    3233

(4 rows)
```

The following example returns a comma\-separated list of sales dates for buyer B\.

```
select listagg(dateid,',') 
within group (order by sellerid desc,salesid asc)
over () as dates
from winsales
where buyerid  = 'b';

             dates                                      
-------------------------------------------
2003-08-02,2004-04-18,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-04-18,2004-02-12

(4 rows)
```

The following example uses DISTINCT to return a list of unique sales dates for buyer B\.

```
select listagg(distinct dateid,',') 
within group (order by sellerid desc,salesid asc)
over () as dates
from winsales
where buyerid  = 'b';

           dates
--------------------------------
2003-08-02,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-02-12
2003-08-02,2004-04-18,2004-02-12

(4 rows)
```

The following example returns a comma\-separated list of sales IDs for each buyer ID\.

```
select buyerid, 
listagg(salesid,',')
within group (order by salesid)
over (partition by buyerid) as sales_id
from winsales
order by buyerid;

   buyerid | sales_id
-----------+------------------------
        a  |10005,40001,40005	
        a  |10005,40001,40005	
        a  |10005,40001,40005	
        b  |20001,30001,30004,30003
        b  |20001,30001,30004,30003
        b  |20001,30001,30004,30003
        b  |20001,30001,30004,30003	
        c  |10001,20002,30007,10006
        c  |10001,20002,30007,10006
        c  |10001,20002,30007,10006
        c  |10001,20002,30007,10006
(11 rows)
```