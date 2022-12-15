# Tutorial: Building customer churn models<a name="tutorial_customer_churn"></a>

In this tutorial, you use Amazon Redshift ML to create a customer churn model with the CREATE MODEL command, and run prediction queries for user scenarios\. Then, you implement queries using the SQL function that the CREATE MODEL command generates\.

You can use a simple CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL statement to specify training data either as a table or SELECT statement\.

This example uses historical information to construct a machine learning model of a mobile operator’s customer churn\. First, SageMaker trains your machine learning model and then tests your model using the profile information of an arbitrary customer\. After the model is validated, Amazon SageMaker deploys the model and the prediction function to Amazon Redshift\. You can use the prediction function to predict whether a customer is going to churn or not\.

## Use case examples<a name="tutorial_customer_churn_tasks"></a>

You can solve other binary classification problems using Amazon Redshift ML, such as predicting if a sales lead will close or not\. You could also predict whether a financial transaction is fraudulent or not\.

**Tasks**
+ Prerequisites
+ Step 1: Load the data from Amazon S3 to Amazon Redshift
+ Step 2: Create the machine learning model
+ Step 3: Perform predictions with the model

## Prerequisites<a name="tutorial_customer_churn_prereqs"></a>

To complete this tutorial, you must have the following prerequisites:
+ You must set up an Amazon Redshift cluster for Amazon Redshift ML\. To do so, use the documentation for [Cluster and configure setup for Amazon Redshift ML administration\.](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html)
+ The Amazon Redshift cluster that you use to create the model, and the Amazon S3 bucket that you use to stage the training data and store the model artifacts must be in the same AWS Region\.
+ To download the SQL commands and the sample dataset used in this documentation, do one of the following:
  + Download the [SQL commands](https://s3.amazonaws.com/redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql), [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv), and [Abalone file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/abalone_xg/abalone.csv)\.
  + Using the AWS CLI for Amazon S3, run the following command\. You can use your own target path\.

    ```
    aws s3 cp s3://redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql </target/path>
    aws s3 cp s3://redshift-downloads/redshift-ml/customer_activity/customer_activity.csv </target/path>
    aws s3 cp s3://redshift-downloads/redshift-ml/abalone_xgb/abalone_xgb.csv </target/path>
    ```

## Step 1: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_customer_churn_step_load"></a>

Use the [Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to edit and run queries and visualize results\.

Running the following queries creates a table named `customer_activity` and ingests the sample dataset from Amazon S3\.

```
DROP TABLE IF EXISTS customer_activity;

CREATE TABLE customer_activity (
state varchar(2),
account_length int,
area_code int,
phone varchar(8),
intl_plan varchar(3),
vMail_plan varchar(3),
vMail_message int,
day_mins float,
day_calls int,
day_charge float,
total_charge float,
eve_mins float,
eve_calls int,
eve_charge float,
night_mins float,
night_calls int,
night_charge float,
intl_mins float,
intl_calls int,
intl_charge float,
cust_serv_calls int,
churn varchar(6),
record_date date
);

COPY customer_activity
FROM 's3://redshift-downloads/redshift-ml/customer_activity/'
REGION 'us-east-1' IAM_ROLE default
FORMAT AS CSV IGNOREHEADER 1;
```

## Step 2: Create the machine learning model<a name="tutorial_customer_churn_step_create_model"></a>

Churn is our target input in this model\. All other inputs for the model are attributes that help to create a function to predict churn\.

The following example uses the CREATE MODEL operation to deliver a model that predicts whether a customer will be active, using inputs such as the customer’s age, postal code, spending, and cases\. In the following example, replace *`DOC-EXAMPLE-BUCKET`* with your own Amazon S3 bucket\.

```
CREATE MODEL customer_churn_auto_model
FROM
    (
      SELECT state,
             account_length,
             area_code,
             total_charge/account_length AS average_daily_spend,
             cust_serv_calls/account_length AS average_daily_cases,
             churn
      FROM customer_activity
      WHERE  record_date < '2020-01-01'
     )
TARGET churn FUNCTION ml_fn_customer_churn_auto
IAM_ROLE default SETTINGS (
  S3_BUCKET '<DOC-EXAMPLE-BUCKET>'
);
```

The SELECT query in the preceding example creates the training data\. The TARGET clause specifies which column is the machine learning label that the CREATE MODEL operation uses to learn how to predict\. The target column “churn” indicates whether the customer still has an active membership or has suspended the membership\. The S3\_BUCKET field is the name of the Amazon S3 bucket that you previously created\. The Amazon S3 bucket is used to share training data and artifacts between Amazon Redshift and Amazon SageMaker\. The remaining columns are the features that are used for the prediction\.

For a summary of the syntax and features of a basic use case of the CREATE MODEL command, see [Simple CREATE MODEL](https://docs.aws.amazon.com/redshift/latest/dg/r_create_model_use_cases.html#r_simple_create_model)\.

### Add permissions for server\-side encryption \(optional\)<a name="tutorial_customer_churn_encryption"></a>

Amazon Redshift by default uses Amazon SageMaker Autopilot for training\. In particular, Amazon Redshift securely exports the training data to the customer\-specified Amazon S3 bucket\. If you don’t specify a `KMS_KEY_ID`, then the data is encrypted using server\-side encryption SSE\-S3 by default\.

When you encrypt your input using server\-side encryption with a AWS KMS managed key \(SSE\-MMS\), then add the following permissions:

```
{
    "Effect": "Allow",
    "Action": [
    "kms:Encrypt"
    "kms:Decrypt"
    ]
}
```

For more information about Amazon SageMaker roles, see [Amazon SageMaker roles](https://docs.aws.amazon.com/sagemaker/latest/dg/sagemaker-roles.html) in the *Amazon SageMaker Developer Guide*\.

### Check the status of model training \(optional\)<a name="tutorial_customer_churn_check_status"></a>

You can use the SHOW MODEL command to know when your model is ready\.

Use the following operation to check the status of the model\.

```
SHOW MODEL customer_churn_auto_model;
```

The following is an example of the output of the previous operation\.

```
+--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
|           Key            |                                                                             Value                                                                             |
+--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        Model Name        |                                                                   customer_churn_auto_model                                                                   |
|       Schema Name        |                                                                            public                                                                             |
|          Owner           |                                                                            awsuser                                                                            |
|      Creation Time       |                                                                   Tue, 14.06.2022 17:15:52                                                                    |
|       Model State        |                                                                           TRAINING                                                                            |
|                          |                                                                                                                                                               |
|      TRAINING DATA:      |                                                                                                                                                               |
|          Query           | SELECT STATE, ACCOUNT_LENGTH, AREA_CODE, TOTAL_CHARGE / ACCOUNT_LENGTH AS AVERAGE_DAILY_SPEND, CUST_SERV_CALLS / ACCOUNT_LENGTH AS AVERAGE_DAILY_CASES, CHURN |
|                          |                                                                    FROM CUSTOMER_ACTIVITY                                                                     |
|                          |                                                               WHERE RECORD_DATE < '2020-01-01'                                                                |
|      Target Column       |                                                                             CHURN                                                                             |
|                          |                                                                                                                                                               |
|       PARAMETERS:        |                                                                                                                                                               |
|        Model Type        |                                                                             auto                                                                              |
|       Problem Type       |                                                                                                                                                               |
|        Objective         |                                                                                                                                                               |
|     AutoML Job Name      |                                                                redshiftml-20220614171552640901                                                                |
|      Function Name       |                                                                   ml_fn_customer_churn_auto                                                                   |
|   Function Parameters    |                                            state account_length area_code average_daily_spend average_daily_cases                                             |
| Function Parameter Types |                                                                 varchar int4 int4 float8 int4                                                                 |
|         IAM Role         |                                                                     default-aws-iam-role                                                                      |
|        S3 Bucket         |                                                                        DOC-EXAMPLE-BUCKET                                                                     |
|       Max Runtime        |                                                                             5400                                                                              |
+--------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

When the model training is complete, the `model_state` variable becomes `Model is Ready`, and the prediction function becomes available\.

## Step 3: Perform predictions with the model<a name="tutorial_customer_churn_step_perform_predictions"></a>

You can use SQL statements to view the predictions made by the prediction model\. In this example, the prediction function created by the CREATE MODEL operation is named `ml_fn_customer_churn_auto`\. The input arguments for the prediction function correspond to the types of the features, such as varchar for the `state` and integer for `account_length`\. The output of the prediction function is the same type as the TARGET column of the CREATE MODEL statement\.

1. You trained the model on data from before 2020\-01\-01, so now you use the prediction function on the testing set\. The following query displays the predictions of whether customers who signed up after 2020\-01\-01 will go through churn or not\.

   ```
   SELECT
       phone,
       ml_fn_customer_churn_auto(
           state,
           account_length,
           area_code,
           total_charge / account_length,
           cust_serv_calls / account_length
       ) AS active
   FROM
       customer_activity
   WHERE
       record_date > '2020-01-01';
   ```

1. The following example uses the same prediction function for a different use case\. In this case, Amazon Redshift predicts the proportion of churners and non\-churners among customers from different states where the record date is greater than 2020\-01\-01\.

   ```
   WITH predicted AS (
       SELECT
           state,
           ml_fn_customer_churn_auto(
               state,
               account_length,
               area_code,
               total_charge / account_length,
               cust_serv_calls / account_length
           ) :: varchar(6) AS active
       FROM
           customer_activity
       WHERE
           record_date > '2020-01-01'
   )
   SELECT
       state,
       SUM(
           CASE
               WHEN active = 'True.' THEN 1
               ELSE 0
           END
       ) AS churners,
       SUM(
           CASE
               WHEN active = 'False.' THEN 1
               ELSE 0
           END
       ) AS nonchurners,
       COUNT(*) AS total_per_state
   FROM
       predicted
   GROUP BY
       state
   ORDER BY
       state;
   ```

1. The following example uses the prediction function for the use case of predicting the percentage of customers who churn in a state\. In this case, Amazon Redshift predicts the churn percentage where the record date is greater than 2020\-01\-01\.

   ```
   WITH predicted AS (
       SELECT
           state,
           ml_fn_customer_churn_auto(
               state,
               account_length,
               area_code,
               total_charge / account_length,
               cust_serv_calls / account_length
           ) :: varchar(6) AS active
       FROM
           customer_activity
       WHERE
           record_date > '2020-01-01'
   )
   SELECT
       state,
       CAST((CAST((SUM(
           CASE
               WHEN active = 'True.' THEN 1
               ELSE 0
           END
       )) AS FLOAT) / CAST(COUNT(*) AS FLOAT)) AS DECIMAL (3, 2)) AS pct_churn,
       COUNT(*) AS total_customers_per_state
   FROM
       predicted
   GROUP BY
       state
   ORDER BY
       3 DESC;
   ```

## Related topics<a name="tutorial_customer_churn_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon RedshiftML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL command](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)