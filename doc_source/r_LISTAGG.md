# LISTAGG function<a name="r_LISTAGG"></a>

For each group in a query, the LISTAGG aggregate function orders the rows for that group according to the ORDER BY expression, then concatenates the values into a single string\. 

LISTAGG is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_LISTAGG-synopsis"></a>

```
LISTAGG( [DISTINCT] aggregate_expression [, 'delimiter' ] ) 
[ WITHIN GROUP (ORDER BY order_list) ]
```

## Arguments<a name="r_LISTAGG-arguments"></a>

DISTINCT  
\(Optional\) A clause that eliminates duplicate values from the specified expression before concatenating\. Trailing spaces are ignored, so the strings `'a'` and `'a '` are treated as duplicates\. LISTAGG uses the first value encountered\. For more information, see [Significance of trailing blanks](r_Character_types.md#r_Character_types-significance-of-trailing-blanks)\.

 *aggregate\_expression*   
 Any valid expression \(such as a column name\) that provides the values to aggregate\. NULL values and empty strings are ignored\. 

 *delimiter*   
\(Optional\) The string constant to separate the concatenated values\. The default is NULL\.

 *WITHIN GROUP \(ORDER BY order\_list\)*   
\(Optional\) A clause that specifies the sort order of the aggregated values\. 

## Returns<a name="r_LISTAGG-data-types"></a>

VARCHAR\(MAX\)\. If the result set is larger than the maximum VARCHAR size \(64K – 1, or 65535\), then LISTAGG returns the following error:

```
Invalid operation: Result size exceeds LISTAGG limit
```

## Usage notes<a name="r_LISTAGG-usage-notes"></a>

If a statement includes multiple LISTAGG functions that use WITHIN GROUP clauses, each WITHIN GROUP clause must use the same ORDER BY values\.

For example, the following statement will return an error\.

```
select listagg(sellerid) 
within group (order by dateid) as sellers,
listagg(dateid) 
within group (order by sellerid) as dates
from winsales;
```

The following statements will execute successfully\.

```
select listagg(sellerid) 
within group (order by dateid) as sellers,
listagg(dateid) 
within group (order by dateid) as dates
from winsales;

select listagg(sellerid) 
within group (order by dateid) as sellers,
listagg(dateid) as dates
from winsales;
```

## Examples<a name="r_LISTAGG-examples"></a>

The following example aggregates seller IDs, ordered by seller ID\. 

```
select listagg(sellerid, ', ') within group (order by sellerid) from sales
where eventid = 4337;
listagg                                                                                                                                 
----------------------------------------------------------------------------------------------------------------------------------------
380, 380, 1178, 1178, 1178, 2731, 8117, 12905, 32043, 32043, 32043, 32432, 32432, 38669, 38750, 41498, 45676, 46324, 47188, 47188, 48294
```

The following example uses DISTINCT to return a list of unique seller IDs\.

```
select listagg(distinct sellerid, ', ') within group (order by sellerid) from sales
where eventid = 4337;

listagg                                                                                    
-------------------------------------------------------------------------------------------
380, 1178, 2731, 8117, 12905, 32043, 32432, 38669, 38750, 41498, 45676, 46324, 47188, 48294
```

The following example aggregates seller IDs in date order\. 

```
select listagg(sellerid) 
within group (order by dateid)
from winsales;

   listagg
-------------
 31141242333
```

The following example returns a pipe\-separated list of sales dates for buyer B\.

```
select listagg(dateid,'|') 
within group (order by sellerid desc,salesid asc)
from winsales
where buyerid  = 'b';

             listagg
---------------------------------------
2003-08-02|2004-04-18|2004-04-18|2004-02-12
```

The following example returns a comma\-separated list of sales IDs for each buyer ID\.

```
select buyerid, 
listagg(salesid,',')
within group (order by salesid) as sales_id
from winsales
group by buyerid
order by buyerid;

   buyerid | sales_id
-----------+------------------------
        a  |10005,40001,40005	
        b  |20001,30001,30004,30003	
        c  |10001,20002,30007,10006
```