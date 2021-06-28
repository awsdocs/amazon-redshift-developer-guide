# Tutorial: customer churn<a name="tutorial_customer_churn"></a>

You can use a simple CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL statement to specify training data either as a table or SELECT statement\.

The following tutorial illustrates an end\-to\-end example of creating a model and running some inference queries for different scenarios using the SQL function that the CREATE MODEL command generates\. The complete SQL script used in this example is available at [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv)\.

When working with Amazon Redshift ML, consider the following:
+ The Amazon Redshift cluster that you use to create the model and the Amazon S3 bucket that is used to stage the training data and model artifacts must be in the same AWS Region\.
+ To view or download the SQL commands used in this documentation and the sample dataset used in the examples, do one of the following:
  + Download the [SQL commands](https://s3.amazonaws.com/redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql), [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv), and [Abalone file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/abalone_xg/abalone.csv)\.
  + Using the AWS CLI for Amazon S3, run the following command\. You can use your own target path\.

    ```
    aws s3 cp s3://redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql /target/path
    aws s3 cp s3://redshift-downloads/redshift-ml/customer_activity/customer_activity.csv /target/path
    aws s3 cp s3://redshift-downloads/redshift-ml/abalone_xgb/abalone_xgb.csv /target/path
    ```

The following queries prepare the training data by creating a table customer\_activity and ingesting data using the sample dataset\.

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
record_date date);

COPY customer_activity
FROM 's3://redshift-downloads/redshift-ml/customer_activity/'
REGION 'us-east-1' IAM_ROLE 'arn:aws:iam::XXXXXXXXXXXX:role/Redshift-ML'
DELIMITER ',' IGNOREHEADER 1;
```

The following example uses the publicly available customer churn prediction dataset from the University of California Irvine Repository of Machine Learning Datasets\. Mobile operators have historical records on which customers ultimately ended up churning and which continued using the service\. The example uses this historical information to construct a machine learning model of one mobile operator’s churn using a process called training\. After training the model, the profile information of an arbitrary customer is used to train the model\. Then Amazon Redshift passes this information to the model and uses the model to predict whether this customer is going to churn\. The dataset for this example is available at [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv)\.

```
CREATE MODEL customer_churn_auto_model FROM (SELECT state,
             account_length,
             area_code,
             total_charge/account_length AS average_daily_spend, 
             cust_serv_calls/account_length AS average_daily_cases,
             churn 
      FROM customer_activity
      WHERE  record_date < '2020-01-01' 
     )
TARGET churn FUNCTION ml_fn_customer_churn_auto
IAM_ROLE 'arn:aws:iam::XXXXXXXXXXXX:role/Redshift-ML'SETTINGS (
  S3_BUCKET 'your-bucket'
);
```

The SELECT query creates the training data\. The TARGET clause specifies which column is the machine learning “label” that the CREATE MODEL uses to learn how to predict\. The remaining columns are the features \(input\) that are used for the prediction\. In this example, the training data provides the feature state, account\_length, area\_code, average daily spending, and average daily cases for customers who have accounts before 2020\-01\-01\. To simplify, the possibility that a customer unsubscribed on the same day that she subscribed is ignored\. The target column “churn” indicates whether the customer still has an active membership or has suspended the membership\.

The CREATE MODEL analyzes the correlations between the input features and the “active” outcome in order to deliver a model that predicts whether a customer will be active, using inputs such as the customer’s age, postal code, spending, and cases\.

Behind the scene, Amazon Redshift uses Amazon SageMaker Autopilot for training by default\. In particular, Amazon Redshift securely exports the training data in the customer specified Amazon S3 bucket\. If you don't specify a KMS\_KEY\_ID, then the data is encrypted using server\-side encryption SSE\-S3 by default\.

When you encrypt your input using server\-side encryption with a AWS KMS\-managed key \(SSE\-MMS\), then add the following permissions:

```
{
    "Effect": "Allow",
    "Action": [
    "kms:Encrypt"
    "kms:Decrypt"
    ]
}
```

For more information about SageMaker roles, see [Amazon SageMaker roles](https://docs.aws.amazon.com/sagemaker/latest/dg/sagemaker-roles.html) in the *Amazon SageMaker Developer Guide*\.

You also need to provide an IAM\_ROLE role for accessing Amazon S3 and Amazon SageMaker\. First, you get all the requirements ready\. Then, you use the CREATE MODEL statement\. At this point, Amazon Redshift can immediately start to use Amazon SageMaker to train and tune the best model for your problem type\.

The CREATE MODEL command operates in an asynchronous mode and returns upon the export of training data to Amazon S3\. The remaining steps of model training and compilation are potentially time\-consuming and continue in the background\. During this operation, you can check the status of training using the [STV\_ML\_MODEL\_INFO](r_STV_ML_MODEL_INFO.md)\. After training completes, you can inspect the model information with the [SHOW MODEL](r_SHOW_MODEL.md)\.

For a summary of the syntax and features of the simple use of CREATE MODEL, see [Simple CREATE MODEL](r_CREATE_MODEL.md#r_simple_create_model)\.

After the training data is exported, the CREATE MODEL command completes\. Training continues in the background\. To check the status of training, use the [STV\_ML\_MODEL\_INFO](r_STV_ML_MODEL_INFO.md)\.

```
select schema_name, model_name, model_state from stv_ml_model_info;

 schema_name |        model_name         |             model_state
-------------+---------------------------+--------------------------------------
 public      | customer_churn_auto_model | Train Model On SageMaker In Progress
(1 row)
```

After the model\_state becomes `Model is Ready`, the function ml\_fn\_customer\_churn\_auto becomes available\. Amazon Redshift uses this function for predictions\. The input arguments of the prediction function correspond to the types of the features\. In this example, the *ml\_fn\_customer\_churn\_auto* inputs a varchar for state, an integer for account\_length, an integer for area code, a decimal for average monthly spend, and a decimal for average monthly cases\. The output of the prediction function is the same type as the TARGET column of the CREATE MODEL statement\.

To perform predictions, simply use the prediction function in your SQL queries to make inference\. For example, the following query predicts whether the recently signed up customers because 2020\-01\-01 go through churn or not\.

```
SELECT phone, 
       ml_fn_customer_churn_auto( 
          state,
          account_length,
          area_code, 
          total_charge/account_length , 
          cust_serv_calls/account_length )
          AS activeFROM customer_activity WHERE record_date > '2020-01-01';
```

The prediction function can appear in any of SQL constructs, including PROJECTION, WHERE, HAVING, GROUP BY, and ORDER BY clauses\.

The following example uses the inference function from the previous CREATE MODEL example for a different user case where Amazon Redshift predicts the proportion of churners and nonchurners among customers from different states because 2020\-01\-01\.

```
WITH inferred AS (SELECT state,
       ml_fn_customer_churn_auto( 
          state,
          account_length,
          area_code, 
          total_charge/account_length, 
          cust_serv_calls/account_length )::varchar(6)
          AS active FROM customer_activity
          WHERE record_date > '2020-01-01' )
SELECT state, SUM(CASE WHEN active = 'True.' THEN 1 ELSE 0 END) AS churners,
       SUM(CASE WHEN active = 'False.' THEN 1 ELSE 0 END) AS nonchurners,
       COUNT(*) AS total_per_state
FROM inferred
GROUP BY state
ORDER BY state;
```