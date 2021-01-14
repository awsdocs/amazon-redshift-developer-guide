# Examples<a name="examples"></a>

The following section illustrates an end\-to\-end example of creating a model and running some inference queries for different scenarios using the SQL function that the CREATE MODEL command generates\. The complete SQL script used in this example is available at [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv)\.

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

The following query creates the training model\.

```
CREATE MODEL customer_churn_auto_model 
FROM (SELECT state,
             account_length,
             area_code,
             total_charge/account_length AS average_daily_spend, 
             cust_serv_calls/account_length AS average_daily_cases,
             churn 
      FROM customer_activity
      WHERE  record_date < '2020-01-01' 
     )
TARGET churn
FUNCTION ml_fn_customer_churn_auto
IAM_ROLE 'arn:aws:iam::XXXXXXXXXXXX:role/Redshift-ML'
SETTINGS (
  S3_BUCKET 'your-bucket'
);
```

Once the model customer\_churn is created, the function ml\_fn\_customer\_churn\_auto becomes available\. The following example uses the inference function from the previous CREATE MODEL example for a different user case where Amazon Redshift predicts the proportion of churners and non\-churners among customers from different states because 2020\-01\-01\.

```
WITH infered AS (SELECT state,
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
FROM infered
GROUP BY state
ORDER BY state;
```