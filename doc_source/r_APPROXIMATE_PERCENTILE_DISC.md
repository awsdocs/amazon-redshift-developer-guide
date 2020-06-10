# APPROXIMATE PERCENTILE\_DISC function<a name="r_APPROXIMATE_PERCENTILE_DISC"></a>

APPROXIMATE PERCENTILE\_DISC is an inverse distribution function that assumes a discrete distribution model\. It takes a percentile value and a sort specification and returns an element from the given set\. Approximation enables the function to execute much faster, with a low relative error of around 0\.5 percent\.

For a given *percentile* value, APPROXIMATE PERCENTILE\_DISC uses a quantile summary algorithm to approximate the discrete percentile of the expression in the ORDER BY clause\. APPROXIMATE PERCENTILE\_DISC returns the value with the smallest cumulative distribution value \(with respect to the same sort specification\) that is greater than or equal to *percentile*\. 

APPROXIMATE PERCENTILE\_DISC is a compute\-node only function\. The function returns an error if the query doesn't reference a user\-defined table or Amazon Redshift system table\.

## Syntax<a name="r_APPROXIMATE_PERCENTILE_DISC-synopsis"></a>

```
APPROXIMATE  PERCENTILE_DISC ( percentile )
WITHIN GROUP (ORDER BY expr)
```

## Arguments<a name="r_APPROXIMATE_PERCENTILE_DISC-arguments"></a>

 *percentile*   
Numeric constant between 0 and 1\. Nulls are ignored in the calculation\.

WITHIN GROUP \( ORDER BY *expr*\)   
Clause that specifies numeric or date/time values to sort and compute the percentile over\. 

## Returns<a name="r_APPROXIMATE_PERCENTILE_DISC-returns"></a>

The same data type as the ORDER BY expression in the WITHIN GROUP clause\.

## Usage notes<a name="r_APPROXIMATE_PERCENTILE_DISC-usage-notes"></a>

If the APPROXIMATE PERCENTILE\_DISC statement includes a GROUP BY clause, the result set is limited\. The limit varies based on node type and the number of nodes\. If the limit is exceeded, the function fails and returns the following error\.

```
GROUP BY limit for approximate percentile_disc exceeded.
```

If you need to evaluate more groups than the limit permits, consider using [PERCENTILE\_CONT function](r_PERCENTILE_CONT.md)\. 

## Examples<a name="r_APPROXIMATE_PERCENTILE_DISC-examples"></a>

The following example returns the number of sales, total sales, and fiftieth percentile value for the top 10 dates\.\. 

```
select top 10 date.caldate,
count(totalprice), sum(totalprice),
approximate percentile_disc(0.5) 
within group (order by totalprice)
from listing
join date on listing.dateid = date.dateid
group by date.caldate
order by 3 desc;

caldate    | count | sum        | percentile_disc
-----------+-------+------------+----------------
2008-01-07 |   658 | 2081400.00 |         2020.00
2008-01-02 |   614 | 2064840.00 |         2178.00
2008-07-22 |   593 | 1994256.00 |         2214.00
2008-01-26 |   595 | 1993188.00 |         2272.00
2008-02-24 |   655 | 1975345.00 |         2070.00
2008-02-04 |   616 | 1972491.00 |         1995.00
2008-02-14 |   628 | 1971759.00 |         2184.00
2008-09-01 |   600 | 1944976.00 |         2100.00
2008-07-29 |   597 | 1944488.00 |         2106.00
2008-07-23 |   592 | 1943265.00 |         1974.00
```