# EXPLAIN\_MODEL function<a name="r_explain_model_function"></a>

The EXPLAIN\_MODEL function returns a SUPER data type that contains a model explainability report in a JSON format\. The explainability report contains information about the Shapley value for all model features\.

The EXPLAIN\_MODEL function currently supports only the AUTO ON or AUTO OFF XGBoost models\.

When the explainability report isn't available, the function returns statuses showing on the progress of the model\. These include `Waiting for training job to complete`, `Waiting for processing job to complete`, and `Processing job failed`\. 

When you run the CREATE MODEL statement, the explanation state becomes `Waiting for training job to complete`\. When the model has been trained and an explanation request is sent, the explanation state becomes `Waiting for processing job to complete`\. When the model explanation completes successfully, the full explainability report is available\. Otherwise, the state becomes `Processing job failed`\.

## Syntax<a name="r_explain_model_function-synopsis"></a>

```
EXPLAIN_MODEL ('schema_name.model_name')
```

## Argument<a name="r_explain_model_function-argument"></a>

 *schema\_name*   
The name of the schema\. If no schema\_name is specified, then the current schema is selected\.

 *model\_name*   
The name of the model\. The model name in a schema must be unique\.

## Return type<a name="r_explain_model_function-return-type"></a>

The EXPLAIN\_MODEL function returns a SUPER data type, as shown following\.

```
{"version":"1.0","explanations":{"kernel_shap":{"label0":{"global_shap_values":{"x0":0.05,"x1":0.10,"x2":0.30,"x3":0.15},"expected_value":0.50}}}}
```

## Examples<a name="r_explain_model_function-examples"></a>

The following example returns the explanation state `waiting for training job to complete`\.

```
select explain_model('customer_churn_auto_model');
                 explain_model
--------------------------------------------------------
{"explanations":"waiting for training job to complete"}
(1 row)
```

When the model explanation completes successfully, the full explainability report is available as follows\.

```
select explain_model('customer_churn_auto_model');
                                       explain_model
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
{"version":"1.0","explanations":{"kernel_shap":{"label0":{"global_shap_values":{"x0":0.05386043365892927,"x1":0.10801289723274592,"x2":0.23227865827017378,"x3":0.0676685133940455,"x4":0.0897097667672375,"x5":0.08502141653270926,"x6":0.07581993936077065,"x7":0.16462880604578135},"expected_value":0.8492974042892456}}}}
(1 row)
```

Because the EXPLAIN\_MODEL function returns the SUPER data type, you can query the explainability report\. By doing this, you can extract `global_shap_values`, `expected_value`, or feature\-specific Shapley values\.

The following example extracts `global_shap_values` for the model\.

```
select json_table.report.explanations.kernel_shap.label0.global_shap_values from (select explain_model('customer_churn_auto_model') as report) as json_table;
                                                       global_shap_values
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
{"state":0.10983770427197151,"account_length":0.1772441398408543,"area_code":0.08626823968639591,"phone":0.0736669595282712,"intl_plan":3.344907436910987,"vmail_plan":0.09646600597854467,"vmail_message":0.2064922655089351,"day_mins":2.015038015251777,"day_calls":0.13179511076780168,"day_charge":0.4941091720480879,"eve_mins":0.46081379198626105,"eve_calls":0.16913440417758477,"eve_charge":0.09651014369401761,"night_mins":0.44218153640050845,"night_calls":0.15311640089218997,"night_charge":0.13850366104495426,"intl_mins":0.7583662464883899,"intl_calls":0.47144468610485685,"intl_charge":0.10945894673611875,"cust_serv_calls":0.31822051038387733}
(1 row)
```

The following example extracts `global_shap_values` for the feature x0\.

```
select json_table.report.explanations.kernel_shap.label0.global_shap_values.x0 from (select explain_model('customer_churn_auto_model') as report) as json_table;
          x0
------------------------
  0.05386043365892927
(1 row)
```

If the model is created in a specific schema and you have access to the created model, then you can query the model explanation as shown following\.

```
-- Check the current schema
SHOW search_path;
   search_path
------------------
  $user, public
(1 row)         
-- If you have the privilege to access the model explanation
-- in `test_schema`
SELECT explain_model('test_schema.test_model_name');
                       explain_model
---------------------------------------------------------
{"explanations":"waiting for training job to complete"}
(1 row)
```