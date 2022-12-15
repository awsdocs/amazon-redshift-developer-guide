# DROP MODEL<a name="r_DROP_MODEL"></a>

Removes a model from the database\. Only the model owner or a superuser can drop a model\. 

DROP MODEL also deletes all the associated prediction function that is derived from this model, all Amazon Redshift artifacts related to the model, and all Amazon S3 data related to the model\. While the model is still being trained in Amazon SageMaker, DROP MODEL will cancel those operations\.

This command isn't reversible\. The DROP MODEL command commits immediately\.

## Required permissions<a name="r_DROP_MODEL-privileges"></a>

Following are required permissions for DROP MODEL:
+ Superuser
+ Users with the DROP MODEL permission
+ Model owner
+ Schema owner

## Syntax<a name="r_DROP_MODEL-synopsis"></a>

```
DROP MODEL [ IF EXISTS ] model_name
```

## Parameters<a name="r_DROP_MODEL-parameters"></a>

 *IF EXISTS*   
A clause that indicates that if the specified schema already exists, the command should make no changes and return a message that the schema exists\.

 *model\_name*   
The name of the model\. The model name in a schema must be unique\.

## Examples<a name="r_DROP_MODEL-examples"></a>

The following example drops the model demo\_ml\.customer\_churn\.

```
DROP MODEL demo_ml.customer_churn 
```