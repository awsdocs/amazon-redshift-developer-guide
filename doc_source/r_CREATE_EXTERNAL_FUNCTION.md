# CREATE EXTERNAL FUNCTION<a name="r_CREATE_EXTERNAL_FUNCTION"></a>

Creates a scalar user\-defined function \(UDF\) based on AWS Lambda for Amazon Redshift\. For more information about Lambda user\-defined functions, see [Creating a scalar Lambda UDF](udf-creating-a-lambda-sql-udf.md)\.

## Syntax<a name="r_CREATE_EXTERNAL_FUNCTION-synopsis"></a>

```
CREATE [ OR REPLACE ] EXTERNAL FUNCTION external_fn_name ( [data_type] [, ...] )
RETURNS data_type
{ VOLATILE | STABLE | IMMUTABLE }   
LAMBDA 'lambda_fn_name' 
IAM_ROLE 'iam-role-arn'
RETRY_TIMEOUT milliseconds;
```

## Parameters<a name="r_CREATE_EXTERNAL_FUNCTION-parameters"></a>

OR REPLACE  
A clause that specifies that if a function with the same name and input argument data types, or *signature*, as this one already exists, the existing function is replaced\. You can only replace a function with a new function that defines an identical set of data types\. You must be a superuser to replace a function\.  
If you define a function with the same name as an existing function but a different signature, you create a new function\. In other words, the function name is overloaded\. For more information, see [Overloading function names](udf-naming-udfs.md#udf-naming-overloading-function-names)\.

*external\_fn\_name*  
The name of the external function\. If you specify a schema name \(such as myschema\.myfunction\), the function is created using the specified schema\. Otherwise, the function is created in the current schema\. For more information about valid names, see [Names and identifiers](r_names.md)\.   
We recommend that you prefix all UDF names with `f_`\. Amazon Redshift reserves the `f_` prefix for UDF names\. By using the `f_` prefix, you help ensure that your UDF name won't conflict with any built\-in SQL function names for Amazon Redshift now or in the future\. For more information, see [Naming UDFs](udf-naming-udfs.md)\.

*data\_type*  
The data type for the input arguments\. For more information, see [Data types](c_Supported_data_types.md)\.

RETURNS *data\_type*  
The data type of the value returned by the function\. The RETURNS data type can be any standard Amazon Redshift data type\. For more information, see [Python UDF data types](udf-data-types.md)\.

VOLATILE \| STABLE \| IMMUTABLE  
A clause that informs the query optimizer about the volatility of the function\.   
To get the best optimization, label your function with the strictest volatility category that is valid for it\. However, if the category is too strict, the optimizer can erroneously skip some calls, resulting in an incorrect result set\. In order of strictness, beginning with the least strict, the volatility categories are as follows:  
+ VOLATILE

  Given the same arguments, the function can return different results on successive calls, even for the rows in a single statement\. The query optimizer can't make any assumptions about the behavior of a volatile function\. A query that uses a volatile function must reevaluate the function for every input row\.
+ STABLE

  Given the same arguments, the function is guaranteed to return the same results for all rows processed within a single statement\. The function can return different results when called in different statements\. This category allows the optimizer to optimize multiple calls of the function within a single statement to a single call for the statement\. 
+ IMMUTABLE

  Given the same arguments, the function always returns the same result, forever\. When a query calls an `IMMUTABLE` function with constant arguments, the optimizer pre\-evaluates the function\.

LAMBDA *'lambda\_fn\_name'*   
 The name of the function that Amazon Redshift calls\.  
For steps to create an AWS Lambda function, see [Create a Lambda function with the console](https://docs.aws.amazon.com/lambda/latest/dg/getting-started-create-function.html) in the *AWS Lambda Developer Guide*\.  
For information regarding permissions required for the Lambda function, see [AWS Lambda permissions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html) in the *AWS Lambda Developer Guide*\.

IAM\_ROLE *'iam\-role\-arn'*   
The Amazon Resource Name \(ARN\) for an AWS Identity and Access Management \(IAM\) role that your cluster uses for authentication and authorization\. The CREATE EXTERNAL FUNCTION command is authorized to invoke Lambda functions through this IAM role\. If your cluster has an existing IAM role with permissions to invoke Lambda functions attached, you can substitute your role's ARN\. For more information, see [Configuring the authorization parameter for Lambda UDFs](udf-creating-a-lambda-sql-udf.md#udf-lambda-authorization)\.  
The following shows the syntax for the IAM\_ROLE parameter\.  

```
IAM_ROLE 'arn:aws:iam::aws-account-id:role/role-name'
```

RETRY\_TIMEOUT *milliseconds*   
The amount of total time in milliseconds that Amazon Redshift uses for the delays in retry backoffs\.   
Instead of retrying immediately for any failed queries, Amazon Redshift performs backoffs and waits for a certain amount of time between retries\. Then Amazon Redshift retries the request to rerun the failed query until the sum of all the delays is equal to or exceeds the RETRY\_TIMEOUT value that you specified\. The default value is 20,000 milliseconds\.  
When a Lambda function is invoked, Amazon Redshift retries for queries that receive errors such as `TooManyRequestsException`, `EC2ThrottledException`, and `ServiceException`\.   
You can set the RETRY\_TIMEOUT parameter to 0 milliseconds to prevent any retries for a Lambda UDF\.

## Examples<a name="r_CREATE_FUNCTION-examples"></a>

Following are examples of using scalar Lambda user\-defined functions \(UDFs\)\.

### Scalar Lambda UDF example using a Node\.js Lambda function<a name="r_CREATE_FUNCTION-lambda-example-node"></a>

The following example creates an external function called `exfunc_sum` that takes two integers as input arguments\. This function returns the sum as an integer output\. The name of the Lambda function to be called is `lambda_sum`\. The language used for this Lambda function is Node\.js 12\.x\. Make sure to specify the IAM role\. The example uses `'arn:aws:iam::123456789012:user/johndoe'` as the IAM role\. We recommend using the STABLE option\.

```
CREATE EXTERNAL FUNCTION exfunc_sum(INT,INT) 
RETURNS INT 
STABLE 
LAMBDA 'lambda_sum' 
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-Exfunc-Test';
```

The Lambda function takes in the request payload and iterates over each row\. All the values in a single row are added to calculate the sum for that row, which is saved in the response array\. The number of rows in the results array is similar to the number of rows received in the request payload\. 

The JSON response payload must have the result data in the 'results' field for it to be recognized by the external function\. The arguments field in the request sent to the Lambda function contains the data payload\. There can be multiple rows in the data payload in case of a batch request\. The following Lambda function iterates over all the rows in the request data payload\. It also individually iterates over all the values within a single row\.

```
exports.handler = async (event) => {
    // The 'arguments' field in the request sent to the Lambda function contains the data payload.
    var t1 = event['arguments'];
    
    // 'len(t1)' represents the number of rows in the request payload.
    // The number of results in the response payload should be the same as the number of rows received.
    const resp = new Array(t1.length);
    
    // Iterating over all the rows in the request payload.
    for (const [i, x] of t1.entries()) 
    {
        var sum = 0;
        // Iterating over all the values in a single row.
        for (const y of x) {
            sum = sum + y;
        }
        resp[i] = sum;
    }
    // The 'results' field should contain the results of the lambda call.
    const response = {
        results: resp
    };
    return JSON.stringify(response);
};
```

The following example calls the external function with literal values\.

```
select exfunc_sum(1,2);
exfunc_sum 
------------
 3
(1 row)
```

The following example creates a table called t\_sum with two columns, c1 and c2, of the integer data type and inserts two rows of data\. Then the external function is called by passing the column names of this table\. The two table rows are sent in a batch request in request payload as a single Lambda invocation\.

```
CREATE TABLE t_sum(c1 int, c2 int);
INSERT INTO t_sum VALUES (4,5), (6,7);
SELECT exfunc_sum(c1,c2) FROM t_sum;
 exfunc_sum 
---------------
 9
 13
(2 rows)
```

### Scalar Lambda UDF example using the RETRY\_TIMEOUT attribute<a name="r_CREATE_FUNCTION-lambda-example-retry"></a>

In the following section, you can find an example of how to use the RETRY\_TIMEOUT attribute in Lambda UDFs\. 

AWS Lambda functions have concurrency limits that you can set for each function\. For more information on concurrency limits, see [Managing concurrency for a Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html) in the *AWS Lambda Developer Guide* and the post [Managing AWS Lambda Function Concurrency](http://aws.amazon.com/blogs/compute/managing-aws-lambda-function-concurrency) on the AWS Compute Blog\. 

When the number of requests being served by a Lambda UDF exceeds the concurrency limits, the new requests receive the `TooManyRequestsException` error\. The Lambda UDF retries on this error until the sum of all the delays between the requests sent to the Lambda function is equal to or exceeds the RETRY\_TIMEOUT value that you set\. The default RETRY\_TIMEOUT value is 20,000 milliseconds\.

The following example uses a Lambda function named `exfunc_sleep_3`\. This function takes in the request payload, iterates over each row, and converts the input to uppercase\. It then sleeps for 3 seconds and returns the result\. The language used for this Lambda function is Python 3\.8\. 

The number of rows in the results array is similar to the number of rows received in the request payload\. The JSON response payload must have the result data in the `results` field for it to be recognized by the external function\. The `arguments` field in the request sent to the Lambda function contains the data payload\. In the case of a batch request, multiple rows can appear in the data payload\.

The concurrency limit for this function is specifically set to 1 in reserved concurrency to demonstrate the use of the RETRY\_TIMEOUT attribute\. When the attribute is set to 1, the Lambda function can only serve one request at a time\.

```
import json
import time
def lambda_handler(event, context):
    t1 = event['arguments']
    # 'len(t1)' represents the number of rows in the request payload.
    # The number of results in the response payload should be the same as the number of rows received.
    resp = [None]*len(t1)

    # Iterating over all rows in the request payload.
    for i, x in enumerate(t1):
        # Iterating over all the values in a single row.
        for j, y in enumerate(x):
            resp[i] = y.upper()
    
    time.sleep(3)
    ret = dict()
    ret['results'] = resp
    ret_json = json.dumps(ret)
    return ret_json
```

Following, two additional examples illustrate the RETRY\_TIMEOUT attribute\. They each invoke a single Lambda UDF\. While invoking the Lambda UDF, each example runs the same SQL query to invoke the Lambda UDF from two concurrent database sessions at the same time\. When first query that invokes the Lambda UDF is being served by the UDF, the second query receives the `TooManyRequestsException` error\. This result occurs because you specifically set the reserved concurrency in the UDF to 1\. For information on how to set reserved concurrency for Lambda functions, see [Configuring reserved concurrency](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html#configuration-concurrency-reservedconfiguration-concurrency-reserved)\.

The first example, following, sets the RETRY\_TIMEOUT attribute for the Lambda UDF to 0 milliseconds\. If the Lambda request receives any exceptions from the Lambda function, Amazon Redshift doesn't make any retries\. This result occurs because the RETRY\_TIMEOUT attribute is set to 0\.

```
CREATE OR REPLACE EXTERNAL FUNCTION exfunc_upper(varchar)
RETURNS varchar
STABLE
LAMBDA 'exfunc_sleep_3'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-Exfunc-Test'
RETRY_TIMEOUT 0;
```

With the RETRY\_TIMEOUT set to 0, you can run the following two queries from separate database sessions to see different results\.

The first SQL query that uses the Lambda UDF runs successfully\.

```
select exfunc_upper('Varchar');
 exfunc_upper  
 --------------
 VARCHAR
(1 row)
```

The second query, which is run from a separate database session at the same time, receives the `TooManyRequestsException` error\.

```
select exfunc_upper('Varchar');
ERROR:  Rate Exceeded.; Exception: TooManyRequestsException; ShouldRetry: 1
DETAIL:
-----------------------------------------------
error:  Rate Exceeded.; Exception: TooManyRequestsException; ShouldRetry: 1
code:      32103
context:query:     0
location:  exfunc_client.cpp:102
process:   padbmaster [pid=26384]
-----------------------------------------------
```

The second example, following, sets the RETRY\_TIMEOUT attribute for the Lambda UDF to 3,000 milliseconds\. Even if the second query is run concurrently, the Lambda UDF retries until the total delays is 3,000 milliseconds\. Thus, both queries run successfully\.

```
CREATE OR REPLACE EXTERNAL FUNCTION exfunc_upper(varchar)
RETURNS varchar
STABLE
LAMBDA 'exfunc_sleep_3'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-Exfunc-Test'
RETRY_TIMEOUT 3000;
```

With the RETRY\_TIMEOUT set to 3,000 milliseconds, you can run the following two queries from separate database sessions to see the same results\.

The first SQL query that runs the Lambda UDF runs successfully\.

```
select exfunc_upper('Varchar');
 exfunc_upper  
 --------------
 VARCHAR
(1 row)
```

The second query runs concurrently, and the Lambda UDF retries until the total delay is 3,000 milliseconds\.

```
select exfunc_upper('Varchar');
 exfunc_upper  
--------------
 VARCHAR
(1 row)
```

### Scalar Lambda UDF example using a Python Lambda function<a name="r_CREATE_FUNCTION-lambda-example-python"></a>

The following example creates an external function that is named `exfunc_multiplication` and that multiplies numbers and returns an integer\. This example incorporates the success and `error_msg` fields in the Lambda response\. The success field is set to false when there is an integer overflow in the multiplication result, and the `error_msg` message is set to `Integer multiplication overflow`\. The `exfunc_multiplication` function takes three integers as input arguments and returns the sum as an integer output\. 

The name of the Lambda function that is called is `lambda_multiplication`\. The language used for this Lambda function is Python 3\.8\. Make sure to specify the IAM role\.

```
CREATE EXTERNAL FUNCTION exfunc_multiplication(int, int, int) 
RETURNS INT 
STABLE 
LAMBDA 'lambda_multiplication' 
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-Exfunc-Test';
```

The Lambda function takes in the request payload and iterates over each row\. All the values in a single row are multiplied to calculate the result for that row, which is saved in the response list\. This example uses a Boolean success value that is set to true by default\. If the multiplication result for a row has an integer overflow, then the success value is set to false\. Then the iteration loop breaks\. 

While creating the response payload, if the success value is false, the following Lambda function adds the `error_msg` field in the payload\. It also sets the error message to `Integer multiplication overflow`\. If the success value is true, then the result data is added in the results field\. The number of rows in the results array, if any, is similar to the number of rows received in the request payload\. 

The arguments field in the request sent to the Lambda function contains the data payload\. There can be multiple rows in the data payload in case of a batch request\. The following Lambda function iterates over all the rows in the request data payload and individually iterates over all the values within a single row\. 

```
import json
def lambda_handler(event, context):
    t1 = event['arguments']
    # 'len(t1)' represents the number of rows in the request payload.
    # The number of results in the response payload should be the same as the number of rows received.
    resp = [None]*len(t1)
    
    # By default success is set to 'True'.
    success = True
    # Iterating over all rows in the request payload.
    for i, x in enumerate(t1):
        mul = 1
        # Iterating over all the values in a single row.
        for j, y in enumerate(x):
            mul = mul*y
            
        # Check integer overflow.
        if (mul >= 9223372036854775807 or mul <= -9223372036854775808):
            success = False
            break
        else:
            resp[i] = mul
    ret = dict()
    ret['success'] = success
    if not success:
        ret['error_msg'] = "Integer multiplication overflow"
    else:
        ret['results'] = resp
    ret_json = json.dumps(ret)
    
    return ret_json
```

The following example calls the external function with literal values\.

```
SELECT exfunc_multiplication(8, 9, 2);
  exfunc_multiplication 
---------------------------
          144
(1 row)
```

The following example creates a table named t\_multi with three columns, c1, c2, and c3, of the integer data type\. The external function is called by passing the column names of this table\. The data is inserted in such a way to cause integer overflow to show how the error is propagated\.

```
CREATE TABLE t_multi (c1 int, c2 int, c3 int);
INSERT INTO t_multi VALUES (2147483647, 2147483647, 4);
SELECT exfunc_multiplication(c1, c2, c3) FROM t_multi;
DETAIL:  
  -----------------------------------------------
  error:  Integer multiplication overflow
  code:      32004context:   
  context:
  query:     38
  location:  exfunc_data.cpp:276
  process:   query2_16_38 [pid=30494]
  -----------------------------------------------
```
