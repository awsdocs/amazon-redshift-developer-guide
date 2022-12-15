# Amazon Redshift ML probability metrics<a name="probability_metrics"></a>

 In supervised learning problems, class labels are outcomes of predictions that use the input data\. For example, if you're using a model to predict whether a customer would resubscribe to a streaming service, possible labels are likely and unlikely\. Redshift ML provides the capability of probability metrics, which assign a probability to each label to indicate its likelihood\. This helps you make more informed decisions based on the predicted outcomes\. In Amazon Redshift ML, probability metrics are available when creating AUTO ON models with a problem type of either binary classification or multiclass classification\. If you omit the AUTO ON parameter, Redshift ML assumes that the model should have AUTO ON\. 

## Create the model<a name="probability_metrics_create_model"></a>

 When creating a model, Amazon Redshift automatically detects the model type and problem type\. If it is a classification problem, Redshift automatically creates a second inference function that you can use to output probabilities relative to each label\. This second inference function's name is your specified inference function name followed by the string `_prob`\. For example, if you name your inference function as `customer_churn_predict`, then the second inference function's name is `customer_churn_predict_prob`\. You can then query this function to get the probabilities of each label\. 

```
CREATE MODEL customer_churn_model
FROM customer_activity
    PROBLEM_TYPE BINARY_CLASSIFICATION
TARGET churn
FUNCTION customer_churn_predict
IAM_ROLE {default}
AUTO ON
SETTINGS ( S3_BUCKET '<DOC-EXAMPLE-BUCKET>'
```

## Get probabilities<a name="probability_metrics_create_model_get_probability"></a>

 Once the probability function is ready, running the command returns a [SUPER type](https://docs.aws.amazon.com/redshift/latest/dg/r_SUPER_type.html) that contains arrays of the returned probabilities and their associated labels\. For example, the result `"probabilities" : [0.7, 0.3], "labels" : ["False.", "True."]` means that the False label has a probability of 0\.7, and the True label has a probability of 0\.3\. 

```
SELECT customer_churn_predict_probabilities(Account_length, Area_code, 
            VMail_message, Day_mins, Day_calls, Day_charge,Eve_mins, Eve_calls, 
            Eve_charge, Night_mins, Night_calls, Night_charge,Intl_mins, Intl_calls, 
            Intl_charge, Cust_serv_calls) 
FROM customer_activity;
            
customer_churn_predict_probabilities
 --------------------
 {"probabilities" : [0.7, 0.3], "labels" : ["False.", "True."]} 
 {"probabilities" : [0.8, 0.2], "labels" : ["False.", "True."]}
 {"probabilities" : [0.75, 0.25], "labels" : ["True.", "False"]}
```

 The probabilities and labels arrays are always sorted by their probabilities in descending order\. You can write a query to return just the predicted label with the highest probability by unnesting the SUPER returned results of the probability function\. 

```
SELECT prediction.labels[0], prediction.probabilities[0]
            FROM (SELECT customer_churn_predict_probabilities(Account_length, Area_code, 
            VMail_message, Day_mins, Day_calls, Day_charge,Eve_mins, Eve_calls, 
            Eve_charge, Night_mins, Night_calls, Night_charge,Intl_mins, Intl_calls, 
            Intl_charge, Cust_serv_calls) AS prediction
FROM customer_activity);

  labels   | probabilities
-----------+--------------
 "False."  | 0.7
 "False."  | 0.8
 "True."   | 0.75
```

To make the queries simpler, you can store the results of the prediction function in a table\.

```
CREATE TABLE churn_auto_predict_probabilities AS 
             (SELECT customer_churn_predict_probabilities(Account_length, Area_code, 
             VMail_message, Day_mins, Day_calls, Day_charge,Eve_mins, Eve_calls, 
             Eve_charge, Night_mins, Night_calls, Night_charge,Intl_mins,
             Intl_calls, Intl_charge, Cust_serv_calls) AS prediction
FROM customer_activity);
```

You can query the table with the results to return only predictions that have a probability higher than 0\.7\.

```
SELECT prediction.labels[0], prediction.probabilities[0]
FROM churn_auto_predict_probabilities
WHERE prediction.probabilities[0] > 0.7;

  labels   | probabilities
-----------+--------------
 "False."  | 0.8
 "True."   | 0.75
```

Using index notation, you can get the probability of a specific label\. The following example returns probabilities of all the `True.` labels\.

```
SELECT label, index, p.prediction.probabilities[index]
FROM churn_auto_predict_probabilities p, p.prediction.labels AS label AT index
WHERE label='True.';

  label  | index | probabilities
---------+-------+---------------
 "True." |     0 | 0.3
 "True." |     0 | 0.2
 "True." |     0 | 0.75
```

 The following example returns all rows that have a `True`\. label with a probability greater than 0\.7, indicating that the customer is likely to churn\. 

```
SELECT prediction.labels[0], prediction.probabilities[0]
FROM churn_auto_predict_probabilities
WHERE prediction.probabilities[0] > 0.7 AND prediction.labels[0] = "True.";

labels     | probabilities
-----------+--------------
 "True."   | 0.75
```