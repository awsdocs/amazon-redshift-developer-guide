# CREATE MODEL<a name="r_CREATE_MODEL"></a>

**Topics**
+ [Prerequisites](#r_create_model_prereqs)
+ [Required privileges](#r_simple_create_model-privileges)
+ [Cost control](#r_create_model_cost)
+ [Full CREATE MODEL](#r_full_create_model)
+ [Parameters](#r_create_model_parameters)
+ [Usage notes](r_create_model_usage_notes.md)
+ [Use cases](r_create_model_use_cases.md)

## Prerequisites<a name="r_create_model_prereqs"></a>

Before you use the CREATE MODEL statement, complete the prerequisites in [Cluster setup for using Amazon Redshift ML](admin-setup.md#cluster-setup)\. The following is a high\-level summary of the prerequisites\.
+ Create an Amazon Redshift cluster with the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\.
+ Attach the AWS Identity and Access Management \(IAM\) policy while creating the cluster\.
+ To allow Amazon Redshift and SageMaker to assume the role to interact with other services, add the appropriate trust policy to the IAM role\.

For details for the IAM role, trust policy, and other prerequisites, see [Cluster setup for using Amazon Redshift ML](admin-setup.md#cluster-setup)\.

Following, you can find different use cases for the CREATE MODEL statement\.
+ [Simple CREATE MODEL](r_create_model_use_cases.md#r_simple_create_model)
+ [CREATE MODEL with user guidance](r_create_model_use_cases.md#r_user_guidance_create_model)
+ [CREATE XGBoost models with AUTO OFF](r_create_model_use_cases.md#r_auto_off_create_model)
+ [Bring your own model \(BYOM\) \- local inference](r_create_model_use_cases.md#r_byom_create_model)
+ [CREATE MODEL with K\-MEANS](r_create_model_use_cases.md#r_k-means_create_model)
+ [Full CREATE MODEL](#r_full_create_model)

## Required privileges<a name="r_simple_create_model-privileges"></a>

Following are required privileges for CREATE MODEL:
+ Superuser
+ Users with the CREATE MODEL privilege
+ Roles with the GRANT CREATE MODEL privilege

## Cost control<a name="r_create_model_cost"></a>

 Amazon Redshift ML uses existing cluster resources to create prediction models, so you don’t have to pay additional costs\. However, you might have additional costs if you need to resize your cluster or want to train your models\. Amazon Redshift ML uses Amazon SageMaker to train models, which does have an additional associated cost\. There are ways to control additional costs, such as limiting the maximum amount of time training can take or by limiting the number of training examples used to train your model\. For more information, see [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)\. 

## Full CREATE MODEL<a name="r_full_create_model"></a>

The following summarizes the basic options of the full CREATE MODEL syntax\.

### Full CREATE MODEL syntax<a name="r_auto_off-create-model-synposis"></a>

The following is the full syntax of the CREATE MODEL statement\.

**Important**  
When creating a model using the CREATE MODEL statement, follow the order of the keywords in the syntax following\.

```
CREATE MODEL model_name
    FROM { table_name | ( select_statement )  | 'job_name' }
    [ TARGET column_name ]   
    FUNCTION function_name ( data_type [, ...] ) 
    IAM_ROLE { default }
    [ AUTO ON / OFF ]
      -- default is AUTO ON 
    [ MODEL_TYPE { XGBOOST | MLP | LINEAR_LEARNER | KMEANS } ]
      -- not required for non AUTO OFF case, default is the list of all supported types
      -- required for AUTO OFF 
    [ PROBLEM_TYPE ( REGRESSION | BINARY_CLASSIFICATION | MULTICLASS_CLASSIFICATION ) ]
      -- not supported when AUTO OFF 
    [ OBJECTIVE ( 'MSE' | 'Accuracy' | 'F1' | 'F1_Macro' | 'AUC' |
                  'reg:squarederror' | 'reg:squaredlogerror'| 'reg:logistic'|
                  'reg:pseudohubererror' | 'reg:tweedie' | 'binary:logistic' | 'binary:hinge',
                  'multi:softmax' ) ]
      -- for AUTO ON: first 5 are valid
      -- for AUTO OFF: 6-13 are valid
    [ PREPROCESSORS 'string' ]
      -- required for AUTO OFF, when it has to be 'none'
      -- optional for AUTO ON
    [ HYPERPARAMETERS { DEFAULT | DEFAULT EXCEPT ( Key 'value' (,...) ) } ]
      -- support XGBoost hyperparameters, except OBJECTIVE
      -- required and only allowed for AUTO OFF
      -- default NUM_ROUND is 100
      -- NUM_CLASS is required if objective is multi:softmax (only possible for AUTO OFF)
     [ SETTINGS (
       S3_BUCKET 'bucket',  |
        -- required 
      KMS_KEY_ID 'kms_string', |
        -- optional
      S3_GARBAGE_COLLECT on / off, |
        -- optional, defualt is on.
      MAX_CELLS integer, |
        -- optional, default is 1,000,000
      MAX_RUNTIME integer (, ...)
        -- optional, default is 5400 (1.5 hours) 
    ) ]
```

## Parameters<a name="r_create_model_parameters"></a>

model\_name  
The name of the model\. The model name in a schema must be unique\.

FROM \{ *table\_name* \| \( *select\_query* \) \| *'job\_name'*\}  
The table\_name or the query that specifies the training data\. They can either be an existing table in the system, or an Amazon Redshift\-compatible SELECT query enclosed with parentheses, that is \(\)\. There must be at least two columns in the query result\. 

TARGET *column\_name*  
The name of the column that becomes the prediction target\. The column must exist in the FROM clause\. 

FUNCTION *function\_name* \( *data\_type* \[, \.\.\.\] \)  
The name of the function to be created and the data types of the input arguments\. You can provide a schema name\.

IAM\_ROLE \{ default \}  
 Use the default keyword to have Amazon Redshift use the IAM role that is set as default and associated with the cluster when the CREAT MODEL command runs\.

\[ AUTO ON / OFF \]  
 Turns on or off CREATE MODEL automatic discovery of preprocessor, algorithm, and hyper\-parameters selection\. 

 *MODEL\_TYPE \{ XGBOOST \| MLP \| LINEAR\_LEARNER \| KMEANS \}*   
\(Optional\) Specifies the model type\. You can specify if you want to train a model of a specific model type, such as XGBoost, multilayer perceptron \(MLP\), KMEANS, or Linear Learner, which are all algorithms that Amazon SageMaker Autopilot supports\. If you don't specify the parameter, then all supported model types are searched during training for the best model\.

 *PROBLEM\_TYPE \( REGRESSION \| BINARY\_CLASSIFICATION \| MULTICLASS\_CLASSIFICATION \)*   
\(Optional\) Specifies the problem type\. If you know the problem type, you can restrict Amazon Redshift to only search of the best model of that specific model type\. If you don't specify this parameter, a problem type is discovered during the training, based on your data\.

OBJECTIVE \( 'MSE' \| 'Accuracy' \| 'F1' \| 'F1Macro' \| 'AUC' \| 'reg:squarederror' \| 'reg:squaredlogerror' \| 'reg:logistic' \| 'reg:pseudohubererror' \| 'reg:tweedie' \| 'binary:logistic' \| 'binary:hinge' \| 'multi:softmax' \| 'rank:pairwise' \| 'rank:ndcg' \)  
\(Optional\) Specifies the name of the objective metric used to measure the predictive quality of a machine learning system\. This metric is optimized during training to provide the best estimate for model parameter values from data\. If you don't specify a metric explicitly, the default behavior is to automatically use MSE: for regression, F1: for binary classification, Accuracy: for multiclass classification\. For more information about objectives, see [AutoMLJobObjective](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_AutoMLJobObjective.html) in the *Amazon SageMaker API Reference* and [Learning task parameters](https://xgboost.readthedocs.io/en/latest/parameter.html#learning-task-parameters)in the XGBOOST documentation\.

 *PREPROCESSORS 'string' *   
\(Optional\) Specifies certain combinations of preprocessors to certain sets of columns\. The format is a list of columnSets, and the appropriate transforms to be applied to each set of columns\. Amazon Redshift applies all the transformers in a specific transformers list to all columns in the corresponding ColumnSet\. For example, to apply OneHotEncoder with Imputer to columns t1 and t2, use the sample command following\.  

```
CREATE MODEL customer_churn
FROM customer_data 
TARGET 'Churn'
FUNCTION predict_churn
IAM_ROLE { default }
PROBLEM_TYPE BINARY_CLASSIFICATION
OBJECTIVE 'F1'
PREPROCESSORS '[
...
  {"ColumnSet": [
      "t1",
      "t2"
    ],
    "Transformers": [
      "OneHotEncoder",
      "Imputer"
    ]
  },
  {"ColumnSet": [
      "t3"
    ],
    "Transformers": [
      "OneHotEncoder"
    ]
  },
  {"ColumnSet": [
      "temp"
    ],
    "Transformers": [
      "Imputer",
      "NumericPassthrough"
    ]
  }
]'
SETTINGS (
  S3_BUCKET 'bucket'
)
```

HYPERPARAMETERS \{ DEFAULT \| DEFAULT EXCEPT \( key ‘value’ \(,\.\.\) \) \}  
Specifies whether the default XGBoost parameters are used or overridden by user\-specified values\. The values must be enclosed with single quotes\. Following are examples of parameters for XGBoost and their defaults\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)

SETTINGS \( S3\_BUCKET *'bucket'*, \| KMS\_KEY\_ID *'kms\_string' *, \| S3\_GARBAGE\_COLLECT on / off, \| MAX\_CELLS integer , \| MAX\_RUNTIME \(,\.\.\.\) \)  
S3\_BUCKET clause specifies the Amazon S3 location that is used to store intermediate results\.  
\(Optional\) KMS\_KEY\_ID specifies if Amazon Redshift uses server\-side encryption with an AWS KMS key to protect data at rest\. Data in transit is protected with Secure Sockets Layer \(SSL\)\.   
\(Optional\) S3\_GARBAGE\_COLLECT \{ ON \| OFF \} specifies whether Amazon Redshift performs garbage collection on the resulting datasets used to train models and the models\. If set to OFF, the resulting datasets used to train models and the models remains in Amazon S3 and can be used for other purposes\. If set to ON, Amazon Redshift deletes the artifacts in Amazon S3 after the training completes\. The default is ON\.  
\(Optional\) MAX\_CELLS specifies the number of cells in the training data\. This value is the product of the number of records \(in the training query or table\) times the number of columns\. The default is 1,000,000\.  
\(Optional\) MAX\_RUNTIME specifies the maximum amount of time to train\. Training jobs often complete sooner depending on dataset size\. This specifies the maximum amount of time the training should take\. The default is 5,400 \(90 minutes\)\.