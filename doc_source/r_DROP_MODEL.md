# DROP MODEL<a name="r_DROP_MODEL"></a>


|  | 
| --- |
| This is prerelease documentation for the machine learning feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Removes a model from the database\. Only the model owner or a superuser can drop a model\. 

DROP MODEL also deletes all prediction functions that are derived from this model, all Amazon Redshift artifacts related to the model, and all Amazon S3 data related to the model\. While the model is still being trained in Amazon SageMaker, DROP MODEL will cancel those operations\.

This command isn't reversible\. The DROP MODEL command commits immediately\.

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