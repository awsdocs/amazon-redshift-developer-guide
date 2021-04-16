# RANDOM function<a name="r_RANDOM"></a>

The RANDOM function generates a random value between 0\.0 \(inclusive\) and 1\.0 \(exclusive\)\. 

## Syntax<a name="r_RANDOM-synopsis"></a>

```
RANDOM()
```

## Return type<a name="r_RANDOM-return-type"></a>

RANDOM returns a DOUBLE PRECISION number\. 

## Usage notes<a name="r_RANDOM_usage_notes"></a>

Call RANDOM after setting a seed value with the [SET](r_SET.md) command to cause RANDOM to generate numbers in a predictable sequence\. 

## Examples<a name="r_RANDOM-examples"></a>

1. Compute a random value between 0 and 99\. If the random number is 0 to 1, this query produces a random number from 0 to 100: 

   ```
   select cast (random() * 100 as int);
   
   int4
   ------
   24
   (1 row)
   ```

1. Retrieve a uniform random sample of 10 items:

   ```
   select * 
   from sales
   order by random()
   limit 10;
   ```

   Now retrieve a random sample of 10 items, but choose the items in proportion to their prices\. For example, an item that is twice the price of another would be twice as likely to appear in the query results:

   ```
   select * 
   from sales
   order by log(1 - random()) / pricepaid
   limit 10;
   ```

1. This example uses the [SET](r_SET.md) command to set a SEED value so that RANDOM generates a predictable sequence of numbers\. 

   First, return three RANDOM integers without setting the SEED value first: 

   ```
   select cast (random() * 100 as int);
   int4
   ------
   6
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   68
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   56
   (1 row)
   ```

   Now, set the SEED value to `.25`, and return three more RANDOM numbers: 

   ```
   set seed to .25;
   select cast (random() * 100 as int);
   int4
   ------
   21
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   79
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   12
   (1 row)
   ```

   Finally, reset the SEED value to `.25`, and verify that RANDOM returns the same results as the previous three calls: 

   ```
   set seed to .25;
   select cast (random() * 100 as int);
   int4
   ------
   21
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   79
   (1 row)
   
   select cast (random() * 100 as int);
   int4
   ------
   12
   (1 row)
   ```