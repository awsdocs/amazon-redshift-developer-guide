# SVL\_UDF\_LOG<a name="r_SVL_UDF_LOG"></a>

 Records system\-defined error and warning messages generating during user\-defined function \(UDF\) execution\. 

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\. 

## Table columns<a name="SVL_UDF_LOG-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_UDF_LOG.html)

## Sample queries<a name="SVL_UDF_LOG-sample-queries"></a>

The following example shows how UDFs handle system\-defined errors\. The first block shows the definition for a UDF function that returns the inverse of an argument\. When you run the function and provide a 0 argument, as the second block shows, the function returns an error\. The third statement reads the error message that is logged in SVL\_UDF\_LOG

```
-- Create a function to find the inverse of a number

CREATE OR REPLACE FUNCTION  f_udf_inv(a int)
  RETURNS float IMMUTABLE
AS $$
   return 1/a
$$ LANGUAGE plpythonu;

-- Run the function with a 0 argument to create an error
Select f_udf_inv(0) from sales;

-- Query SVL_UDF_LOG to view the message

Select query, created, message::varchar
from svl_udf_log;

 query |          created           | message                             
-------+----------------------------+---------------------------------------------------------
  2211 | 2015-08-22 00:11:12.04819  | ZeroDivisionError: long division or modulo by zero\nNone
```

The following example adds logging and a warning message to the UDF so that a divide by zero operation results in a warning message instead of stopping with an error message\. 

```
-- Create a function to find the inverse of a number and log a warning

CREATE OR REPLACE FUNCTION f_udf_inv_log(a int)
  RETURNS float IMMUTABLE
 AS $$ 
  import logging
  logger = logging.getLogger() #get root logger
  if a==0:
    logger.warning('You attempted to divide by zero.\nReturning zero instead of error.\n') 
    return 0
  else:
     return 1/a
$$ LANGUAGE plpythonu;
```

The following example runs the function, then queries SVL\_UDF\_LOG to view the message\.

```
-- Run the function with a 0 argument to trigger the warning
Select f_udf_inv_log(0) from sales;

-- Query SVL_UDF_LOG to view the message

Select query, created, message::varchar
from svl_udf_log;

query |          created           | message                             
------+----------------------------+----------------------------------
    0 | 2015-08-22 00:11:12.04819  | You attempted to divide by zero. 
                                     Returning zero instead of error.
```