# LISTAGG window function<a name="r_WF_LISTAGG"></a>

For each group in a query, the LISTAGG window function orders the rows for that group according to the ORDER BY expression, then concatenates the values into a single string\. 

LISTAGG is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_WF_LISTAGG-synopsis"></a>

```
LISTAGG( [DISTINCT] expression [, 'delimiter' ] ) 
[ WITHIN GROUP (ORDER BY order_list) ] 
OVER ( [PARTITION BY partition_expression] )
```

## Arguments<a name="r_WF_LISTAGG-arguments"></a>

DISTINCT  
\(Optional\) A clause that eliminates duplicate values from the specified expression before concatenating\. Trailing spaces are ignored, so the strings `'a'` and `'a '` are treated as duplicates\. LISTAGG uses the first value encountered\. For more information, see [Significance of trailing blanks](r_Character_types.md#r_Character_types-significance-of-trailing-blanks)\.

  

*aggregate\_expression*   
 Any valid expression \(such as a column name\) that provides the values to aggregate\. NULL values and empty strings are ignored\. 

 *delimiter*   
\(Optional\) The string constant to will separate the concatenated values\. The default is NULL\.

 WITHIN GROUP \(ORDER BY *order\_list*\)   
\(Optional\) A clause that specifies the sort order of the aggregated values\. Deterministic only if ORDER BY provides unique ordering\. The default is to aggregate all rows and return a single value\.

 OVER   
 A clause that specifies the window partitioning\. The OVER clause cannot contain a window ordering or window frame specification\.

 PARTITION BY *partition\_expression*   
\(Optional\) Sets the range of records for each group in the OVER clause\.

## Returns<a name="r_WF_LISTAGG-data-types"></a>

VARCHAR\(MAX\)\. If the result set is larger than the maximum VARCHAR size \(64K – 1, or 65535\), then LISTAGG returns the following error:

```
Invalid operation: Result size exceeds LISTAGG limit
```

## Examples<a name="r_WF_LISTAGG-examples"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Overview example for window functions](c_Window_functions.md#r_Window_function_example)\. 

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