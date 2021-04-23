# Getting started with Amazon Redshift ML<a name="getting-started-machine-learning"></a>

Amazon Redshift ML makes it easy for SQL users to create, train, and deploy machine learning models using familiar SQL commands\. Amazon Redshift ML enables you to use your data in Amazon Redshift cluster to train model with Amazon SageMaker\. Subsequently, the models can be localized and predictions can be made within an Amazon Redshift database\. Training and predictions can be performed by anyone without any expert knowledge in machine learning\.

Amazon Redshift ML offers different features to different approaches:
+ As a machine learning beginner, you use the AUTO ON without user guidance\. This path allows SageMaker Autopilot to automatically pick preprocessors for features and select the algorithm and problem type, such as binary classification, multi\-class classification\.
+ As a machine learning advanced practitioner, you use the AUTO ON with user guidance\. This path still goes through SageMaker Autopilot, but you can restrict training space by specifying candidate preprocessors, problem types, or objectives\.
+ As a machine learning expert, you use the direct SageMaker path and choose AUTO OFF\. You can specify hyperparameters for XGBoost\-based training\.
+ As a data engineer, you can bring a pretrained XGBoost model in SageMaker and import it into Amazon Redshift for local inference\.

As a Amazon Redshift ML user, you can choose any of the following options to train and deploy your model\.
+ Problem types, see [CREATE MODEL with user guidance](r_CREATE_MODEL.md#r_user_guidance_create_model)\.
+ Objectives, see [CREATE MODEL with user guidance](r_CREATE_MODEL.md#r_user_guidance_create_model) or [CREATE XGBoost models with AUTO OFF](r_CREATE_MODEL.md#r_auto_off_create_model)\.
+ Model types, see [CREATE XGBoost models with AUTO OFF](r_CREATE_MODEL.md#r_auto_off_create_model)\.
+ Preprocessors, see [CREATE MODEL with user guidance](r_CREATE_MODEL.md#r_user_guidance_create_model)\.
+ Hyperparameters, see [CREATE XGBoost models with AUTO OFF](r_CREATE_MODEL.md#r_auto_off_create_model)\.
+ Bring your own model \(BYOM\), see [Bring your own model \(BYOM\)](r_CREATE_MODEL.md#r_byom_create_model)\.

Read the following sections to understand the prerequisites of setting up your Amazon Redshift cluster, permissions, and ownership for using Amazon Redshift ML\. These sections also describe how simple training and predictions work in Amazon Redshift ML\.

**Topics**
+ [Cluster setup for using Amazon Redshift ML](cluster-setup.md)
+ [Managing permissions and ownership](permissions-ownership.md)
+ [Simple training](simple-training.md)
+ [Prediction](prediction.md)