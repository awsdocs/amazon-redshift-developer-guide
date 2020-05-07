# Step 1: Create an external table that contains nested data<a name="tutorial-nested-data-create-table"></a>

To create the external table for this tutorial, run the following command\. 

```
CREATE EXTERNAL TABLE spectrum.customers (
  id     int,
  name   struct<given:varchar(20), family:varchar(20)>,
  phones array<varchar(20)>,
  orders array<struct<shipdate:timestamp, price:double precision>>
)
STORED AS PARQUET
LOCATION 's3://awssampledbuswest2/nested_example/customers/';
```

In the example preceding, the external table `spectrum.customers` uses the `struct` and `array` data types to define columns with nested data\. Amazon Redshift Spectrum supports querying nested data in Parquet, ORC, JSON, and Ion file formats\. The `LOCATION` parameter has to refer to the Amazon S3 folder that contains the nested data or files\. 

**Note**  
Amazon Redshift doesn't support complex data types in an Amazon Redshift database table\. You can use complex data types only with Redshift Spectrum external tables\.

You can nest `array` and `struct` types at any level\. For example, you can define a column named `toparray` as shown in the following example\.

```
toparray array<struct<nestedarray:
         array<struct<morenestedarray: 
         array<string>>>>>
```

You can also nest `struct` types as shown for column `x` in the following example\.

```
x struct<a: string,
         b: struct<c: integer,
                   d: struct<e: string>
                  >
        >
```