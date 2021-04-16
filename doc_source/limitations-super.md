# Limitations<a name="limitations-super"></a>

When using SUPER data type, you can find related limitations following:
+ You can't define SUPER columns as either a distribution or sort key\.
+ Aggregations involving SUPER columns aren't supported and will follow in upcoming patch updates\. 
+ The SUPER data type only supports up to 1MB of data for individual rows\.
+ You can't perform partial update or transform operations on SUPER columns\.
+ You can't use the SUPER data type and its alias in right joins or full outer joins\.
+ Manifest option isn't supported with UNLOAD queries involving super column references\.
+ Casting limitations

  SUPER values can be cast to and from other data types, with the following exceptions:
  + Amazon Redshift doesn't differentiate integers and decimals of scale 0\.
  + If the scale isn't zero, SUPER data type has the same behavior as other Amazon Redshift data types, except that Amazon Redshift converts SUPER\-related errors to null, as shown in the following example\.

    ```
    SELECT 5::bool;
     bool
    -------
     True
    (1 row)
    
    SELECT 5::decimal::bool;
    ERROR:  cannot cast type numeric to boolean
    
    SELECT 5::super::bool;
     bool
    -------
     True
    (1 row)
    
    SELECT 5.0::bool;
    ERROR:  cannot cast type numeric to boolean
    
    SELECT 5.0::super::bool;
     bool
    -------
    (1 row)
    ```
  + Amazon Redshift doesn't cast the date and time types to SUPER data type\. Amazon Redshift can only cast the date and time data types from SUPER data type, as shown in the following example\.

    ```
    SELECT o.o_orderdate FROM customer_orders_lineitem c,c.c_orders o;
      order_date
    ------------------
     "2001-09-08"
    (1 row)
    
    
    SELECT JSON_TYPEOF(o.o_orderdate) FROM customer_orders_lineitem c,c.c_orders o;
     json_typeof
    -----------------
     string
    (1 row)
    
    
    SELECT o.o_orderdate::date FROM customer_orders_lineitem c,c.c_orders o;
     order_date
    ----------------
     2001-09-08
    (1 row)
    
    
    --date/time cannot be cast to super 
    SELECT '2019-09-09'::date::super;
    ERROR:  cannot cast type date to super
    ```
  + Cast from non\-scalar values \(object and array\) to string returns NULL\. To properly serialize these non\-scalar values, don't cast them\. Instead, use `json_serialize` to cast non\-scalar values\. The `json_serialize` function returns a varchar\. Typically, you don't need to cast non\-scalar values to varchar since Amazon Redshift implicitly serializes as shown in the following first example\. 

    ```
    SELECT r_nations FROM region_nations WHERE r_regionkey=300;
       r_nations
    ----------------
     [1,"abc",null]
    (1 row)
    
    SELECT r_nations::varchar FROM region_nations WHERE r_regionkey=300;
     r_nations
    -------------
    (1 row)
    
    SELECT JSON_SERIALIZE(r_nations) FROM region_nations WHERE r_regionkey=300;
     json_serialize
    -----------------
     [1,"abc",null]
    (1 row)
    ```