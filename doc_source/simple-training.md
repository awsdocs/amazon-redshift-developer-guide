# Simple training<a name="simple-training"></a>

You can use a simple CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL statement to specify training data either as a table or SELECT statement\.

The following example uses the publicly available customer churn prediction dataset from the University of California Irvine Repository of Machine Learning Datasets\. Mobile operators have historical records on which customers ultimately ended up churning and which continued using the service\. The example uses this historical information to construct a machine learning model of one mobile operator’s churn using a process called training\. After training the model, the profile information of an arbitrary customer is used to train the model\. Then Amazon Redshift passes this information to the model and uses the model to predict whether this customer is going to churn\. The dataset for this example is available at [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv)\.

```
CREATE MODEL customer_churn_auto_model 
FROM (SELECT state,
             account_length,
             area_code,
             total_charge/account_length AS average_daily_spend, 
             cust_serv_calls/account_length AS average_daily_cases,
             churn 
      FROM customer_activity
      WHERE  record_date < '2020-01-01')
TARGET churn 
FUNCTION ml_fn_customer_churn_auto
IAM_ROLE 'arn:aws:iam::XXXXXXXXXXXX:role/Redshift-ML' 
SETTINGS (
  S3_BUCKET 'your-bucket'
);
```

The SELECT query creates the training data\. The TARGET clause specifies which column is the machine learning “label” that the CREATE MODEL uses to learn how to predict\. The remaining columns are the features \(input\) that are used for the prediction\. In this example, the training data provides the feature state, account\_length, area\_code, average daily spending, and average daily cases for customers who have accounts before 2020\-01\-01\. To simplify, the possibility that a customer unsubscribed on the same day that she subscribed is ignored\. The target column “churn” indicates whether the customer still has an active membership or has suspended the membership\.

The CREATE MODEL analyzes the correlations between the input features and the “active” outcome in order to deliver a model that predicts whether a customer will be active, using inputs such as the customer’s age, postal code, spending, and cases\.

Behind the scene, Amazon Redshift typically uses Amazon SageMaker Autopilot for training\. In particular, Amazon Redshift securely exports the training data in the customer specified Amazon S3 bucket\. If you don't specify a KMS\_KEY\_ID, then the data is encrypted using server\-side encryption SSE\-S3 by default\. You also need to provide an IAM\_ROLE role for accessing Amazon S3 and Amazon SageMaker\. First, you get all the requirements ready\. Then, you use the CREATE MODEL statement\. At this point, Amazon Redshift can immediately start to use Amazon SageMaker to train and tune the best model for your problem type\.

The training of the model might take a longer time because many activities happen behind the scenes\. For more information, see [How Amazon Redshift ML works with Amazon SageMaker](working_with_sagemaker.md)\. However, the CREATE MODEL statement execution returns after the model is trained and is ready to use\. Then you can check the SHOW MODEL\. For more information about SHOW MODEL, see [SHOW MODEL](r_SHOW_MODEL.md)\.

For a summary of the syntax and features of the simple use of CREATE MODEL, see [Simple CREATE MODEL](r_CREATE_MODEL.md#r_simple_create_model)\.