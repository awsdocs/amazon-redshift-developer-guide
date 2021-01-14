# Prediction<a name="prediction"></a>

From the CREATE MODEL example in [Simple CREATE MODEL](r_CREATE_MODEL.md#r_simple_create_model), the result is a new SQL function, named *ml\_fn\_customer\_churn\_auto*\. Amazon Redshift uses this function for predictions\. The input arguments of the prediction function correspond to the types of the features\. In this example, the *ml\_fn\_customer\_churn\_auto* inputs an varchar for state, an integer for account\_length, an integer for area code, a decimal for average monthly spend, and a decimal for average monthly cases\. The output of the prediction function is the same type as the TARGET column of the CREATE MODEL statement\.

To perform predictions, simply use the prediction function in your SQL queries to make inference\. For example, the following query predicts whether the recently signed up customers because 2020\-01\-01 go through churn or not\.

```
SELECT phone, 
       ml_fn_customer_churn_auto( 
          state,
          account_length,
          area_code, 
          total_charge/account_length , 
          cust_serv_calls/account_length )
          AS active
FROM customer_activity
WHERE record_date > '2020-01-01';
```

The prediction function can appear in any of SQL constructs, including PROJECTION, WHERE, HAVING, GROUP BY, and ORDER BY clauses\.