# Launch your AWS CloudFormation stack and then query your data in Amazon S3<a name="c-getting-started-using-spectrum-query-s3-data-cfn"></a>

After you create an Amazon Redshift cluster and connect to the cluster, you can install your Redshift Spectrum DataLake AWS CloudFormation template and then query your data\.

CloudFormation installs the Redshift Spectrum Getting Started DataLake template and creates a stack that includes the following: 
+ A role named `myspectrum_role` associated with your Redshift cluster
+ An external schema named `myspectrum_schema`
+ An external table named `sales` in an Amazon S3 bucket
+ A Redshift table named `event` loaded with data

**To launch your Redshift Spectrum Getting Started DataLake CloudFormation stack**

1. Choose [https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=DataLake&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/DataLake.yml](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=DataLake&templateURL=https://s3.amazonaws.com/redshift-downloads/docs-downloads/DataLake.yml)\.

   You can also download and customize the Redshift Spectrum Getting Started DataLake CloudFormation [CFN template](https://s3.amazonaws.com/redshift-downloads/docs-downloads/DataLake.yml), then open [AWS CloudFormation](https://aws.amazon.com/cloudformation) and install the customized template\.

   The CloudFormation console opens with the DataLake\.yml template selected\.

1. Choose **Next**\.

1. Under **Parameters**, enter the Amazon Redshift cluster name, database name, and your database user name\.

1. Choose **Next**\.

   The stack options appear\.

1. Choose **Next** to accept the default settings\.

1. Review the information and under **Capabilities**, and choose **I acknowledge that AWS CloudFormation might create IAM resources**\.

1. Choose **Create stack**\.

If an error occurs while the stack is being created, see the following information:
+ View the CloudFormation **Events** tab for information that can help you resolve the error\.
+ Delete the DataLake CloudFormation stack before trying the operation again\.
+ Make sure that you are connected to your Amazon Redshift database\.
+ Make sure that you entered the correct information for the Amazon Redshift cluster name, database name, and database user name\.

## Querying your data in Amazon S3<a name="c-getting-started-spectrum-cfn-query-s3-data"></a>

You query external tables using the same SELECT statements that you use to query other Amazon Redshift tables\. These SELECT statement queries include joining tables, aggregating data, and filtering on predicates\. 

The following query returns the number of rows in the `myspectrum_schema.sales` external table\. 

```
select count(*) from myspectrum_schema.sales;
```

```
count 
------
172462
```

## Joining an external table with a local table<a name="c-getting-started-spectrum-cfn-table-join"></a>

The following example joins the external table `myspectrum_schema.sales` with the local table `event` to find the total sales for the top 10 events\.

```
select top 10 myspectrum_schema.sales.eventid, sum(myspectrum_schema.sales.pricepaid) from myspectrum_schema.sales, event
where myspectrum_schema.sales.eventid = event.eventid
and myspectrum_schema.sales.pricepaid > 30
group by myspectrum_schema.sales.eventid
order by 2 desc;
```

```
eventid | sum     
--------+---------
    289 | 51846.00
   7895 | 51049.00
   1602 | 50301.00
    851 | 49956.00
   7315 | 49823.00
   6471 | 47997.00
   2118 | 47863.00
    984 | 46780.00
   7851 | 46661.00
   5638 | 46280.00
```

## Viewing the query plan<a name="c-getting-started-spectrum-cfn-query-plan"></a>

View the query plan for the previous query\. Note the `S3 Seq Scan`, `S3 HashAggregate`, and `S3 Query Scan` steps that were run on the data on Amazon S3\.

```
explain
select top 10 myspectrum_schema.sales.eventid, sum(myspectrum_schema.sales.pricepaid) 
from myspectrum_schema.sales, event
where myspectrum_schema.sales.eventid = event.eventid
and myspectrum_schema.sales.pricepaid > 30
group by myspectrum_schema.sales.eventid
order by 2 desc;
```

```
QUERY PLAN                                                                                                                                                                                
-----------------------------------------------------------------------------
XN Limit  (cost=1001055770628.63..1001055770628.65 rows=10 width=31)                                                                                                                      
  ->  XN Merge  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                               
        Merge Key: sum(sales.derived_col2)                                                                                                                                                
        ->  XN Network  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                       
              Send to leader                                                                                                                                                              
              ->  XN Sort  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                    
                    Sort Key: sum(sales.derived_col2)                                                                                                                                     
                    ->  XN HashAggregate  (cost=1055770620.49..1055770620.99 rows=200 width=31)                                                                                           
                          ->  XN Hash Join DS_BCAST_INNER  (cost=3119.97..1055769620.49 rows=200000 width=31)                                                                             
                                Hash Cond: ("outer".derived_col1 = "inner".eventid)                                                                                                       
                                ->  XN S3 Query Scan sales  (cost=3010.00..5010.50 rows=200000 width=31)                                                                                  
                                      ->  S3 HashAggregate  (cost=3010.00..3010.50 rows=200000 width=16)                                                                                  
                                            ->  S3 Seq Scan spectrum.sales location:"s3://awssampledbuswest2/tickit/spectrum/sales" format:TEXT  (cost=0.00..2150.00 rows=172000 width=16)
                                                  Filter: (pricepaid > 30.00)                                                                                                             
                                ->  XN Hash  (cost=87.98..87.98 rows=8798 width=4)                                                                                                        
                                      ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=4)
```