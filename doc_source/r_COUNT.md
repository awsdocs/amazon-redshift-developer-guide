# COUNT function<a name="r_COUNT"></a>

 The COUNT function counts the rows defined by the expression\.

The COUNT function has three variations\. COUNT \( \* \) counts all the rows in the target table whether they include nulls or not\. COUNT \( *expression* \) computes the number of rows with non\-NULL values in a specific column or expression\. COUNT \( DISTINCT *expression* \) computes the number of distinct non\-NULL values in a column or expression\.

## Syntax<a name="r_COUNT-synopsis"></a>

```
[ APPROXIMATE ] COUNT ( [ DISTINCT | ALL ] * | expression )
```

## Arguments<a name="r_COUNT-arguments"></a>

 *expression *   
The target column or expression that the function operates on\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values from the specified expression before doing the count\. With the argument ALL, the function retains all duplicate values from the expression for counting\. ALL is the default\.

APPROXIMATE  
When used with APPROXIMATE, a COUNT \( DISTINCT *expression* \) function uses a HyperLogLog algorithm to approximate the number of distinct non\-NULL values in a column or expression\. Queries that use the APPROXIMATE keyword execute much faster, with a low relative error of around 2%\. Approximation is warranted for queries that return a large number of distinct values, in the millions or more per query, or per group, if there is a group by clause\. For smaller sets of distinct values, in the thousands, approximation might be slower than a precise count\. APPROXIMATE can only be used with COUNT \( DISTINCT \)\.

## Data types<a name="c_Supported_data_types_count"></a>

The COUNT function supports all argument data types\.

The COUNT function returns BIGINT\.

## Examples<a name="r_COUNT-examples"></a>

Count all of the users from the state of Florida:

```
select count (*) from users where state='FL';

count
-------
510
(1 row)
```

Count all of the unique venue IDs from the EVENT table:

```
select count (distinct venueid) as venues from event;

venues
--------
204
(1 row)
```

Count the number of times each seller listed batches of more than four tickets for sale\. Group the results by seller ID:

```
select count(*), sellerid from listing 
where numtickets > 4
group by sellerid
order by 1 desc, 2;

count | sellerid
-------+----------
12 |    6386
11 |    17304
11 |    20123
11 |    25428
...
```

The following examples compare the return values and execution times for COUNT and APPROXIMATE COUNT\. 

```
select  count(distinct pricepaid) from sales;
count
-------
  4528
(1 row)

Time: 48.048 ms

select approximate count(distinct pricepaid) from sales;
count
-------
  4541
(1 row)

Time: 21.728 ms
```