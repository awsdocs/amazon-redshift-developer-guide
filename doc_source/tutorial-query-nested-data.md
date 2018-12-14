# Tutorial: Querying Nested Data with Amazon Redshift Spectrum<a name="tutorial-query-nested-data"></a>

## Overview<a name="tutorial-nested-data-overview"></a>

Amazon Redshift Spectrum supports querying nested data in Parquet, ORC, JSON, and Ion file formats\. Redshift Spectrum accesses the data using external tables\. You can create external tables that use the complex data types `struct`, `array`, and `map`\.

For example, suppose that your data file contains the following data in Amazon S3 in a folder named `customers`\.

```
{ Id: 1,
  Name:   {Given:"John", Family:"Smith"},
  Phones: ["123-457789"],
  Orders: [ {Date: "Mar 1,2018 11:59:59", Price: 100.50}
            {Date: "Mar 1,2018 09:10:00", Price: 99.12} ]
}
{ Id: 2,
  Name:   {Given:"Jenny", Family:"Doe"},
  Phones: ["858-8675309", "415-9876543"],
  Orders: [ ] 
}
{ Id: 3,
  Name: {Given:"Andy", Family:"Jones"},
  Phones: [ ]
  Orders: [ {Date: "Mar 2,2018 08:02:15", Price: 13.50} ] 
}
```

You can use Redshift Spectrum to query this data\. The following tutorial shows you how to do so\.

For tutorial prerequisites, steps, and nested data use cases, see the following topics:
+ [Prerequisites](#tutorial-nested-data-prereq)
+ [Step 1: Create an External Table That Contains Nested Data](tutorial-nested-data-create-table.md)
+ [Step 2: Query Your Nested Data in Amazon S3 with SQL Extensions](tutorial-query-nested-data-sqlextensions.md)
+ [Nested Data Use Cases ](nested-data-use-cases.md)
+ [Nested Data Limitations](nested-data-restrictions.md)

### Prerequisites<a name="tutorial-nested-data-prereq"></a>

If you are not using Redshift Spectrum yet, follow the steps in the [Getting Started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md) tutorial before continuing\.