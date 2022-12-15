# Tutorial: Building multi\-class classification models<a name="tutorial_multi-class_classification"></a>

In this tutorial, you use Amazon Redshift ML to create a machine learning model that solves multi\-class classification problems\. The multi\-class classification algorithm classifies data points into one of three or more classes\. Then, you implement queries using the SQL function that the CREATE MODEL command generates\.

You can use a CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL operation to specify training data either as a table or a SELECT statement\.

To follow along with the tutorial, you use the public dataset [E\-Commerce Sales Forecast](https://www.kaggle.com/allunia/e-commerce-sales-forecast), which includes sales data of an online UK retailer\. The model you generate will target the most active customers for a special customer loyalty program\. With multi\-class classification, you can use the model to predict how many months a customer will be active over a 13\-month period\. The prediction function designates customers who are predicted to be active for 7 or more months for admission to the program\.

## Use case examples<a name="tutorial_multi-class_classification_tasks"></a>

You can solve other multi\-class classification problems with Amazon Redshift ML, such as predicting the best\-selling product from a product line\. You could also predict which fruit an image contains, such as selecting apples or pears or oranges\.

**Tasks**
+ Prerequisites
+ Step 1: Load the data from Amazon S3 to Amazon Redshift
+ Step 2: Create the machine learning model
+ Step 3: Perform predictions with the model

## Prerequisites<a name="tutorial_multi-class_classification_prereqs"></a>

To complete this tutorial, you must complete the [Administrative setup](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html) for Amazon Redshift ML\.

## Step 1: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_multi-class_classification_step_load"></a>

Use the [Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to run the following queries\. These queries load the sample data into Amazon Redshift\.

1. The following query creates a table named `ecommerce_sales`\.

   ```
   CREATE TABLE IF NOT EXISTS ecommerce_sales (
       invoiceno VARCHAR(30),
       stockcode VARCHAR(30),
       description VARCHAR(60),
       quantity DOUBLE PRECISION,
       invoicedate VARCHAR(30),
       unitprice DOUBLE PRECISION,
       customerid BIGINT,
       country VARCHAR(25)
   );
   ```

1. The following query copies the sample data from the [E\-Commerce Sales Forecast dataset](https://www.kaggle.com/allunia/e-commerce-sales-forecast) into the `ecommerce_sales`table\.

   ```
   COPY ecommerce_sales
   FROM
       's3://redshift-ml-multiclass/ecommerce_data.txt' 
   IAM_ROLE default 
   DELIMITER '\t' 
   IGNOREHEADER 1 
   REGION 'us-east-1' 
   MAXERROR 100;
   ```

### Split the data<a name="tutorial_multi-class_classification_split_data"></a>

When you create a model in Amazon Redshift ML, SageMaker automatically splits your data into training and test sets, so that SageMaker can determine the model accuracy\. By manually splitting the data at this step, you will be able to verify the accuracy of the model by allocating an additional prediction set\. 

Use the following SQL statement to split the data into three sets for training, validation, and prediction\.

```
--creates table with all data
CREATE TABLE ecommerce_sales_data AS (
    SELECT
        t1.stockcode,
        t1.description,
        t1.invoicedate,
        t1.customerid,
        t1.country,
        t1.sales_amt,
        CAST(RANDOM() * 100 AS INT) AS data_group_id
    FROM
        (
            SELECT
                stockcode,
                description,
                invoicedate,
                customerid,
                country,
                SUM(quantity * unitprice) AS sales_amt
            FROM
                ecommerce_sales
            GROUP BY
                1,
                2,
                3,
                4,
                5
        ) t1
);

--creates training set
CREATE TABLE ecommerce_sales_training AS (
    SELECT
        a.customerid,
        a.country,
        a.stockcode,
        a.description,
        a.invoicedate,
        a.sales_amt,
        (b.nbr_months_active) AS nbr_months_active
    FROM
        ecommerce_sales_data a
        INNER JOIN (
            SELECT
                customerid,
                COUNT(
                    DISTINCT(
                        DATE_PART(y, CAST(invoicedate AS DATE)) || '-' || LPAD(
                            DATE_PART(mon, CAST(invoicedate AS DATE)),
                            2,
                            '00'
                        )
                    )
                ) AS nbr_months_active
            FROM
                ecommerce_sales_data
            GROUP BY
                1
        ) b ON a.customerid = b.customerid
    WHERE
        a.data_group_id < 80
);

--creates validation set
CREATE TABLE ecommerce_sales_validation AS (
    SELECT
        a.customerid,
        a.country,
        a.stockcode,
        a.description,
        a.invoicedate,
        a.sales_amt,
        (b.nbr_months_active) AS nbr_months_active
    FROM
        ecommerce_sales_data a
        INNER JOIN (
            SELECT
                customerid,
                COUNT(
                    DISTINCT(
                        DATE_PART(y, CAST(invoicedate AS DATE)) || '-' || LPAD(
                            DATE_PART(mon, CAST(invoicedate AS DATE)),
                            2,
                            '00'
                        )
                    )
                ) AS nbr_months_active
            FROM
                ecommerce_sales_data
            GROUP BY
                1
        ) b ON a.customerid = b.customerid
    WHERE
        a.data_group_id BETWEEN 80
        AND 90
);

--creates prediction set
CREATE TABLE ecommerce_sales_prediction AS (
    SELECT
        customerid,
        country,
        stockcode,
        description,
        invoicedate,
        sales_amt
    FROM
        ecommerce_sales_data
    WHERE
        data_group_id > 90);
```

## Step 2: Create the machine learning model<a name="tutorial_multi-class_classification_step_create_model"></a>

In this step, you use the CREATE MODEL statement to create your machine learning model using multi\-class classification\. 

The following query creates the multi\-class classification model with the training set using the CREATE MODEL operation\. Replace *`DOC-EXAMPLE-BUCKET`* with your own Amazon S3 bucket\.

```
CREATE MODEL ecommerce_customer_activity
FROM
    (
        SELECT
            customerid,
            country,
            stockcode,
            description,
            invoicedate,
            sales_amt,
            nbr_months_active
        FROM
            ecommerce_sales_training
    ) TARGET nbr_months_active FUNCTION predict_customer_activity IAM_ROLE default PROBLEM_TYPE MULTICLASS_CLASSIFICATION SETTINGS (
        S3_BUCKET '<DOC-EXAMPLE-BUCKET>',
        S3_GARBAGE_COLLECT OFF
    );
```

In this query, you specify the problem type as `Multiclass_Classification`\. The target that you predict for the model is `nbr_months_active`\. When SageMaker finishes training the model, it creates the function `predict_customer_activity`, which you will use to make predictions in Amazon Redshift\.

### Show the status of model training \(optional\)<a name="tutorial_multi-class_classification_show_status"></a>

You can use the SHOW MODEL command to know when your model is ready\.

Use the following query to return various metrics of the model, including model state and accuracy\.

```
SHOW MODEL ecommerce_customer_activity;
```

When the model is ready, the output of the previous operation should show that the `Model State` is `Ready`\. The following is an example of the output of the SHOW MODEL operation\.

```
+--------------------------+-----------------------------------------------------------------------------------------------+
|        Model Name        |                                  ecommerce_customer_activity                                  |
+--------------------------+-----------------------------------------------------------------------------------------------+
|       Schema Name        |                                            public                                             |
|          Owner           |                                            awsuser                                            |
|      Creation Time       |                                   Fri, 17.06.2022 19:02:15                                    |
|       Model State        |                                             READY                                             |
|   Training Job Status    |                                  MaxAutoMLJobRuntimeReached                                   |
|   validation:accuracy    |                                           0.991280                                            |
|      Estimated Cost      |                                           7.897689                                            |
|                          |                                                                                               |
|      TRAINING DATA:      |                                                                                               |
|          Query           | SELECT CUSTOMERID, COUNTRY, STOCKCODE, DESCRIPTION, INVOICEDATE, SALES_AMT, NBR_MONTHS_ACTIVE |
|                          |                                 FROM ECOMMERCE_SALES_TRAINING                                 |
|      Target Column       |                                       NBR_MONTHS_ACTIVE                                       |
|                          |                                                                                               |
|       PARAMETERS:        |                                                                                               |
|        Model Type        |                                            xgboost                                            |
|       Problem Type       |                                   MulticlassClassification                                    |
|        Objective         |                                           Accuracy                                            |
|     AutoML Job Name      |                                redshiftml-20220617190215268770                                |
|      Function Name       |                                   predict_customer_activity                                   |
|   Function Parameters    |                customerid country stockcode description invoicedate sales_amt                 |
| Function Parameter Types |                          int8 varchar varchar varchar varchar float8                          |
|         IAM Role         |                                     default-aws-iam-role                                      |
|        S3 Bucket         |                                         DOC-EXAMPLE-BUCKET                                    |
|       Max Runtime        |                                             5400                                              |
+--------------------------+-----------------------------------------------------------------------------------------------+
```

## Step 3: Perform predictions with the model<a name="tutorial_multi-class_classification_step_perform_predictions"></a>

The following query shows which customers qualify for your customer loyalty program\. If the model predicts that the customer will be active for at least seven months, then the model selects the customer for the loyalty program\.

```
SELECT
    customerid,
    predict_customer_activity(
        customerid,
        country,
        stockcode,
        description,
        invoicedate,
        sales_amt
    ) AS predicted_months_active
FROM
    ecommerce_sales_prediction
WHERE
    predicted_months_active >= 7
GROUP BY
    1,
    2
LIMIT
    10;
```

### Run prediction queries against the validation data \(optional\)<a name="tutorial_multi-class_classification_run_validation_prediction"></a>

Run the following prediction queries against the validation data to see the model’s level of accuracy\.

```
SELECT
    CAST(SUM(t1.match) AS decimal(7, 2)) AS predicted_matches,
    CAST(SUM(t1.nonmatch) AS decimal(7, 2)) AS predicted_non_matches,
    CAST(SUM(t1.match + t1.nonmatch) AS decimal(7, 2)) AS total_predictions,
    predicted_matches / total_predictions AS pct_accuracy
FROM
    (
        SELECT
            customerid,
            country,
            stockcode,
            description,
            invoicedate,
            sales_amt,
            nbr_months_active,
            predict_customer_activity(
                customerid,
                country,
                stockcode,
                description,
                invoicedate,
                sales_amt
            ) AS predicted_months_active,
            CASE
                WHEN nbr_months_active = predicted_months_active THEN 1
                ELSE 0
            END AS match,
            CASE
                WHEN nbr_months_active <> predicted_months_active THEN 1
                ELSE 0
            END AS nonmatch
        FROM
            ecommerce_sales_validation
    )t1;
```

### Predict how many customers miss entry \(optional\)<a name="tutorial_multi-class_classification_run_missed_entries"></a>

The following query compares the number of customers that are predicted to be active for only 5 or 6 months\. The model predicts that these customers will miss out on the loyalty program\. The query then compares the amount that barely miss the program to the number that are predicted to be eligible for the loyalty program\. This query could be used to inform a decision on whether to lower the threshold for the loyalty program\. You can also determine if there is a significant amount of customers that are predicted to barely miss out on the program\. You could then encourage those customers to increase their activity to get a loyalty program membership\.

```
SELECT
    predict_customer_activity(
        customerid,
        country,
        stockcode,
        description,
        invoicedate,
        sales_amt
    ) AS predicted_months_active,
    COUNT(customerid)
FROM
    ecommerce_sales_prediction
WHERE
    predicted_months_active BETWEEN 5 AND 6
GROUP BY
    1
ORDER BY
    1 ASC
LIMIT
    10)
UNION
(SELECT
      NULL AS predicted_months_active,
    COUNT (customerid)
FROM 
    ecommerce_sales_prediction
WHERE
    predict_customer_activity(
        customerid,
        country,
        stockcode,
        description,
        invoicedate,
        sales_amt
    ) >=7);
```

## Related topics<a name="tutorial_multi-class_classification_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL operation](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)