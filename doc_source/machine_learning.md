# Using machine learning in Amazon Redshift \(preview\)<a name="machine_learning"></a>


|  | 
| --- |
| This is prerelease documentation for the machine learning feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

Amazon Redshift machine learning \(Amazon Redshift ML\) is a robust, cloud\-based service that makes it easy for analysts and data scientists of all skill levels to use machine learning technology\. You provide the data that you want to train a model and metadata associated with data inputs to Amazon Redshift\. Then Amazon Redshift ML creates models that capture patterns in the input data\. You can then use these models to generate predictions for new input data without incurring additional costs\.

When working with the preview, consider the following:
+ New Amazon Redshift clusters must be created with the **SQL\_PREVIEW** maintenance track\. For more information about preview tracks, see [Choosing cluster maintenance tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks)\.
+ This feature is currently available for test purposes only\. Don't use the feature for production use cases\.
+ The syntax and command behavior are subject to change during the preview\.
+ You can't switch an existing Amazon Redshift cluster from the current or trailing track to this preview track, or vice versa\. However, you can restore snapshots from a cluster running on the current or trailing track to SQL\_PREVIEW track\.
+ The machine learning preview period is expected to run until March 31, 2021\. 
+ The Amazon Redshift cluster that you use to create the model and the Amazon S3 bucket that is used to stage the training data and model artifacts must be in the same AWS Region\.
+ To view or download the SQL commands used in this documentation and the sample dataset used in the examples, do one of the following:
  + Download the [SQL commands](https://s3.amazonaws.com/redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql), [Customer activity file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/customer_activity/customer_activity.csv), and [Abalone file](https://s3.amazonaws.com/redshift-downloads/redshift-ml/abalone_xg/abalone.csv)\.
  + Using the AWS Amazon S3 CLI, run the following command\. You can use your own target path\.

    ```
    aws s3 cp s3://redshift-downloads/redshift-ml/tutorial-scripts/redshift-ml-tutorial.sql /target/path
    aws s3 cp s3://redshift-downloads/redshift-ml/customer_activity/customer_activity.csv /target/path
    aws s3 cp s3://redshift-downloads/redshift-ml/abalone_xg/abalone.csv /target/path
    ```
+ For any questions, issues, or feedback related to the preview features during the preview period, email `redshift-ml@amazon.com` or open a support case with AWS Support\. 

**Topics**
+ [Machine learning overview](machine_learning_overview.md)
+ [Getting started with Amazon Redshift ML](geting-started-machine-learning.md)
+ [Examples](examples.md)
+ [Costs for using Amazon Redshift ML](cost.md)
+ [How Amazon Redshift ML works with Amazon SageMaker](working_with_sagemaker.md)