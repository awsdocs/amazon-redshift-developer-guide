# LN function<a name="r_LN"></a>

Returns the natural logarithm of the input parameter\. Synonym of the DLOG1 function\. 

Synonym of [DLOG1 function](r_DLOG1.md)\. 

## Syntax<a name="r_LN-synopsis"></a>

```
LN(expression)
```

## Argument<a name="r_LN-argument"></a>

 *expression*   
The target column or expression that the function operates on\.   
This function returns an error for some data types if the expression references an Amazon Redshift user\-created table or an Amazon Redshift STL or STV system table\. 
Expressions with the following data types produce an error if they reference a user\-created or system table\. Expressions with these data types run exclusively on the leader node:   
+ BOOLEAN 
+ CHAR 
+ DATE 
+ DECIMAL or NUMERIC 
+ TIMESTAMP 
+ VARCHAR 
Expressions with the following data types run successfully on user\-created tables and STL or STV system tables:   
+ BIGINT 
+ DOUBLE PRECISION 
+ INTEGER 
+ REAL 
+ SMALLINT 

## Return type<a name="r_LN-return-type"></a>

The LN function returns the same type as the expression\. 

## Example<a name="r_LN-example"></a>

The following example returns the natural logarithm, or base e logarithm, of the number 2\.718281828: 

```
select ln(2.718281828);
ln
--------------------
0.9999999998311267
(1 row)
```

Note that the answer is nearly equal to 1\. 

This example returns the natural logarithm of the values in the USERID column in the USERS table: 

```
select username, ln(userid) from users order by userid limit 10;

 username |        ln
----------+-------------------
 JSG99FHE |                 0
 PGL08LJI | 0.693147180559945
 IFT66TXU |  1.09861228866811
 XDZ38RDD |  1.38629436111989
 AEB55QTM |   1.6094379124341
 NDQ15VBM |  1.79175946922805
 OWY35QYB |  1.94591014905531
 AZG78YIP |  2.07944154167984
 MSD36KVR |  2.19722457733622
 WKW41AIW |  2.30258509299405
(10 rows)
```