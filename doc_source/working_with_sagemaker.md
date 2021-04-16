# How Amazon Redshift ML works with Amazon SageMaker<a name="working_with_sagemaker"></a>

Amazon Redshift works with Amazon SageMaker Autopilot in order to automatically obtain the best model and make the prediction function available in Amazon Redshift\.

The following diagram illustrates how Amazon Redshift ML works\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/machine_learning_overview.png)

The general workflow is as follows:

1. Amazon Redshift exports the training data into Amazon S3\. 

1. Amazon SageMaker Autopilot preprocesses the training data\. Preprocessing performs important functions, such as imputing missing values\. It recognizes that certain columns are categorical \(such as the postal code\), properly formats them for training, and performs numerous other tasks\. Choosing the best preprocessors to apply on the training dataset is a problem in itself, and Amazon SageMaker Autopilot automates its solution\.

1. Amazon SageMaker Autopilot finds the algorithm and algorithm hyperparameters that deliver the model with the most accurate predictions\.

1. Amazon Redshift registers the prediction function as a SQL function in your Amazon Redshift cluster\.

1. When you run CREATE MODEL statements, Amazon Redshift uses Amazon SageMaker for training\. Therefore, there is an associated cost for training your model\. This is a separate line item for Amazon SageMaker in your AWS bill\. You also pay for the storage used in Amazon S3 for storing your training data\. Inference using models created with CREATE MODEL that can be compiled and run on your Amazon Redshift cluster won't be charged\. There are no additional Amazon Redshift charges for using Amazon Redshift ML\.