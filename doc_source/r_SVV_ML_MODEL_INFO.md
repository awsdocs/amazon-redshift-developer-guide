# SVV\_ML\_MODEL\_INFO<a name="r_SVV_ML_MODEL_INFO"></a>

State information about the current state of the machine learning model\.

SVV\_ML\_MODEL\_INFO is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVV_ML_MODEL_INFO-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_ML_MODEL_INFO.html)

## Sample query<a name="r_SVV_ML_MODEL_INFO-sample-query"></a>

The following query displays the current state of machine learning models\.

```
SELECT schema_name, model_name, model_state 
FROM svv_ml_model_info;

 schema_name |        model_name            |             model_state
-------------+------------------------------+--------------------------------------
 public      | customer_churn_auto_model    | Train Model On SageMaker In Progress
 public      | customer_churn_xgboost_model | Model is Ready
(2 row)
```