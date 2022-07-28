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
+ [Step 1: Create an external table that contains nested data](tutorial-nested-data-create-table.md)
+ [Step 2: Query your nested data in Amazon S3 with SQL extensions](tutorial-query-nested-data-sqlextensions.md)
+ [Nested data use cases ](nested-data-use-cases.md)
+ [Nested data limitations](nested-data-restrictions.md)
+ [Serializing complex nested JSON](serializing-complex-JSON.md)

### Prerequisites<a name="tutorial-nested-data-prereq"></a>

If you are not using Redshift Spectrum yet, follow the steps in the [Getting started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md) tutorial before continuing\.