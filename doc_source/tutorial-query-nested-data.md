# Tutorial: Querying nested data with Amazon Redshift Spectrum<a name="tutorial-query-nested-data"></a>

## Overview<a name="tutorial-nested-data-overview"></a>

Amazon Redshift Spectrum supports querying nested data in Parquet, ORC, JSON, and Ion file formats\. Redshift Spectrum accesses the data using external tables\. You can create external tables that use the complex data types `struct`, `array`, and `map`\.

For example, suppose that your data file contains the following data in Amazon S3 in a folder named `customers`\. Although there isn't a single root element, each JSON object in this sample data represents a row in a table\. 

```
{"id": 1,
 "name": {"given": "John", "family": "Smith"},
 "phones": ["123-457789"],
 "orders": [{"shipdate": "2018-03-01T11:59:59.000Z", "price": 100.50},
            {"shipdate": "2018-03-01T09:10:00.000Z", "price": 99.12}]
}
{"id": 2,
 "name": {"given": "Jenny", "family": "Doe"},
 "phones": ["858-8675309", "415-9876543"],
 "orders": []
}
{"id": 3,
 "name": {"given": "Andy", "family": "Jones"},
 "phones": [],
 "orders": [{"shipdate": "2018-03-02T08:02:15.000Z", "price": 13.50}]
}
```

You can use Redshift Spectrum to query this data\. The following tutorial shows you how to do so\.

For tutorial prerequisites, steps, and nested data use cases, see the following topics:
+ [Prerequisites](#tutorial-nested-data-prereq)
+ [Step 1: Create an external table that contains nested data](#tutorial-nested-data-create-table)
+ [Step 2: Query your nested data in Amazon S3 with SQL extensions](#tutorial-query-nested-data-sqlextensions)
+ [Nested data use cases ](nested-data-use-cases.md)
+ [Nested data limitations](nested-data-restrictions.md)
+ [Serializing complex nested JSON](serializing-complex-JSON.md)

### Prerequisites<a name="tutorial-nested-data-prereq"></a>

If you are not using Redshift Spectrum yet, follow the steps in the [Getting started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md) before continuing\.

To create an external schema, replace the IAM role ARN in the following command with the role ARN you created in [Create an IAM role](c-getting-started-using-spectrum-create-role.md)\. Then run the command in your SQL client\.

```
create external schema spectrum 
from data catalog 
database 'myspectrum_db' 
iam_role 'arn:aws:iam::123456789012:role/myspectrum_role'
create external database if not exists;
```

## Step 1: Create an external table that contains nested data<a name="tutorial-nested-data-create-table"></a>

You can view the [source data](https://s3.amazonaws.com/redshift-downloads/tickit/spectrum/customers/customer_file1) by downloading it from Amazon S3\. 

To create the external table for this tutorial, run the following command\. 

```
CREATE EXTERNAL TABLE spectrum.customers (
  id     int,
  name   struct<given:varchar(20), family:varchar(20)>,
  phones array<varchar(20)>,
  orders array<struct<shipdate:timestamp, price:double precision>>
)
STORED AS PARQUET
LOCATION 's3://redshift-downloads/tickit/spectrum/customers/';
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

## Step 2: Query your nested data in Amazon S3 with SQL extensions<a name="tutorial-query-nested-data-sqlextensions"></a>

Redshift Spectrum supports querying `array`, `map`, and `struct` complex types through extensions to the Amazon Redshift SQL syntax\. 

### Extension 1: Access to columns of structs<a name="nested-data-sqlextension1"></a>

You can extract data from `struct` columns using a dot notation that concatenates field names into paths\. For example, the following query returns given and family names for customers\. The given name is accessed by the long path `c.name.given`\. The family name is accessed by the long path `c.name.family`\. 

```
SELECT c.id, c.name.given, c.name.family
FROM   spectrum.customers c;
```

The preceding query returns the following data\.

```
id | given | family
---|-------|-------
1  | John  | Smith
2  | Jenny | Doe
3  | Andy  | Jones
(3 rows)
```

A `struct` can be a column of another `struct`, which can be a column of another `struct`, at any level\. The paths that access columns in such deeply nested `struct`s can be arbitrarily long\. For example, see the definition for the column `x` in the following example\.

```
x struct<a: string,
         b: struct<c: integer, 
                      d: struct<e: string>
                  >
        >
```

You can access the data in `e` as `x.b.d.e`\.

**Note**  
You use `struct`s only to describe the path to the fields that they contain\. You can't access them directly in a query or return them from a query\.

### Extension 2: Ranging over arrays in a FROM clause<a name="nested-data-sqlextension2"></a>

You can extract data from `array` columns \(and, by extension, `map` columns\) by specifying the `array` columns in a `FROM` clause in place of table names\. The extension applies to the `FROM` clause of the main query, and also the `FROM` clauses of subqueries\. You can't reference `array` elements by position, such as `c.orders[0]`\. 

By combining ranging over `arrays` with joins, you can achieve various kinds of unnesting, as explained in the following use cases\. 

#### Unnesting using inner joins<a name="unnest-inner-joins"></a>

The following query selects customer IDs and order ship dates for customers that have orders\. The SQL extension in the FROM clause `c.orders o` depends on the alias `c`\.

```
SELECT c.id, o.shipdate
FROM   spectrum.customers c, c.orders o
```

For each customer `c` that has orders, the `FROM` clause returns one row for each order `o` of the customer `c`\. That row combines the customer row `c` and the order row `o`\. Then the `SELECT` clause keeps only the `c.id` and `o.shipdate`\. The result is the following\.

```
id|      shipdate
--|----------------------
1 |2018-03-01  11:59:59
1 |2018-03-01  09:10:00
3 |2018-03-02  08:02:15
(3 rows)
```

The alias `c` provides access to the customer fields, and the alias `o` provides access to the order fields\. 

The semantics are similar to standard SQL\. You can think of the `FROM` clause as running the following nested loop, which is followed by `SELECT` choosing the fields to output\. 

```
for each customer c in spectrum.customers
  for each order o in c.orders
     output c.id and o.shipdate
```

Therefore, if a customer doesn't have an order, the customer doesn't appear in the result\.

You can also think of this as the `FROM` clause performing a `JOIN` with the `customers` table and the `orders` array\. In fact, you can also write the query as shown in the following example\.

```
SELECT c.id, o.shipdate
FROM   spectrum.customers c INNER JOIN c.orders o ON true
```

**Note**  
If a schema named `c` exists with a table named `orders`, then `c.orders` refers to the table `orders`, and not the array column of `customers`\.

#### Unnesting using left joins<a name="unnest-left-joins"></a>

The following query outputs all customer names and their orders\. If a customer hasn't placed an order, the customer's name is still returned\. However, in this case the order columns are NULL, as shown in the following example for Jenny Doe\.

```
SELECT c.id, c.name.given, c.name.family, o.shipdate, o.price
FROM   spectrum.customers c LEFT JOIN c.orders o ON true
```

The preceding query returns the following data\.

```
id  |  given  | family  |    shipdate          | price
----|---------|---------|----------------------|--------
 1  |  John   | Smith   | 2018-03-01  11:59:59 | 100.5
 1  |  John   | Smith   | 2018-03-01  09:10:00 |  99.12
 2  |  Jenny  | Doe     |                      |
 3  |  Andy   | Jones   | 2018-03-02  08:02:15 |  13.5
 (4 rows)
```

### Extension 3: Accessing an array of scalars directly using an alias<a name="nested-data-sqlextension3"></a>

When an alias `p` in a `FROM` clause ranges over an array of scalars, the query refers to the values of `p` simply as `p`\. For example, the following query produces pairs of customer names and phone numbers\.

```
SELECT c.name.given, c.name.family, p AS phone
FROM   spectrum.customers c LEFT JOIN c.phones p ON true
```

The preceding query returns the following data\.

```
given  |  family  |  phone
-------|----------|-----------
John   | Smith    | 123-4577891
Jenny  | Doe      | 858-8675309
Jenny  | Doe      | 415-9876543
Andy   | Jones    | 
(4 rows)
```

### Extension 4: Accessing elements of maps<a name="nested-data-sqlextension4"></a>

Redshift Spectrum treats the `map` data type as an `array` type that contains `struct` types with a `key` column and a `value` column\. The `key` must be a `scalar`; the value can be any data type\. 

For example, the following code creates an external table with a `map` for storing phone numbers\.

```
CREATE EXTERNAL TABLE spectrum.customers2 (
  id     int,
  name   struct<given:varchar(20), family:varchar(20)>,
  phones map<varchar(20), varchar(20)>,
  orders array<struct<shipdate:timestamp, price:double precision>>
)
STORED AS PARQUET
LOCATION 's3://redshift-downloads/tickit/spectrum/customers/';
```

Because a `map` type behaves like an `array` type with columns `key` and `value`, you can think of the preceding schemas as if they were the following\.

```
CREATE EXTERNAL TABLE spectrum.customers3 (
  id     int,
  name   struct<given:varchar(20), family:varchar(20)>,
  phones array<struct<key:varchar(20), value:varchar(20)>>,
  orders array<struct<shipdate:timestamp, price:double precision>>
)
STORED AS PARQUET
LOCATION 's3://redshift-downloads/tickit/spectrum/customers/';
```

The following query returns the names of customers with a mobile phone number and returns the number for each name\. The map query is treated as the equivalent of querying a nested `array` of `struct` types\. The following query only returns data if you have created the external table as described previously\. 

```
SELECT c.name.given, c.name.family, p.value 
FROM   spectrum.customers c, c.phones p 
WHERE  p.key = 'mobile'
```

**Note**  
The `key` for a `map` is a `string` for Ion and JSON file types\.