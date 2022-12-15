# Tutorial: Building regression models with linear learner<a name="tutorial_linear_learner_regression"></a>

In this tutorial, you create a linear learner model with data from Amazon S3 and run prediction queries with the model using Amazon Redshift ML\. The SageMaker linear learner algorithm solves either regression or multi\-class classification problems\. To learn more about regression and multi\-class classification problems, see [Problem types for the machine learning paradigms](https://docs.aws.amazon.com/sagemaker/latest/dg/algorithms-choose.html#basic-machine-learning-paradigms) in the Amazon SageMaker Developer Guide\. In this tutorial, you solve a regression problem\. The linear learner algorithm trains many models in parallel, and automatically determines the most optimized model\. You use the CREATE MODEL operation in Amazon Redshift, which creates your linear learner model using SageMaker and sends a prediction function to Amazon Redshift\. For more information about the linear learner algorithm, see [Linear Learner Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/linear-learner.html) in the Amazon SageMaker Developer Guide\.

You can use a CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL operation to specify training data either as a table or a SELECT statement\.

Linear learner models optimize either continuous objectives or discrete objectives\. Continuous objectives are used for regression, while discrete variables are used for classification\. Some methods provide a solution for only continuous objectives, such as the regression method\. The linear learner algorithm provides an increase in speed over naive hyperparameter optimization techniques, such as the Naive Bayes technique\. A naive optimization technique assumes that each input variable is independent\. To use the linear learner algorithm, you must provide columns representing the dimensions of the inputs, and rows representing the observations\. For more information about the linear learner algorithm, see the [Linear Learner Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/linear-learner.html) in the Amazon SageMaker Developer Guide\. 

In this tutorial, you build a linear learner model that predicts the age of abalone\. You use the CREATE MODEL command on the [Abalone dataset](http://archive.ics.uci.edu/ml/datasets/Abalone) to determine the relationship between the physical measurements of abalone\. Then, you use the model to determine the age of abalone\.

## Use case examples<a name="tutorial_linear_learner_regression_tasks"></a>

You can solve other regression problems with linear learner and Amazon Redshift ML, such as predicting the price of a house\. You could also use Redshift ML to predict the number of people who will use a city’s bike rental service\.

**Tasks**
+ Prerequisites
+ Step 1: Load the data from Amazon S3 to Amazon Redshift
+ Step 2: Create the machine learning model
+ Step 3: Validate the model

## Prerequisites<a name="tutorial_linear_learner_regression_prereqs"></a>

To complete this tutorial, you must complete the [Administrative setup](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html) for Amazon Redshift ML\.

## Step 1: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_linear_learner_regression_step_load_data"></a>

Use the [Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to run the following queries\. These queries load the sample data into Redshift and divide the data into a training set and a validation set\.

1. The following query creates the `abalone_dataset` table\.

   ```
   CREATE TABLE abalone_dataset (
       id INT IDENTITY(1, 1),
       Sex CHAR(1),
       Length float,
       Diameter float,
       Height float,
       Whole float,
       Shucked float,
       Viscera float,
       Shell float,
       Rings integer
   );
   ```

1. The following query copies the sample data from the [Abalone dataset](http://archive.ics.uci.edu/ml/datasets/Abalone) in Amazon S3 to the `abalone_dataset` table you created previously in Amazon Redshift\.

   ```
   COPY abalone_dataset
   FROM
       's3://redshift-ml-multiclass/abalone.csv' REGION 'us-east-1' IAM_ROLE default CSV IGNOREHEADER 1 NULL AS 'NULL';
   ```

1. By manually splitting the data, you will be able to verify the accuracy of the model by allocating an additional prediction set\. The following query splits the data into two sets\. The `abalone_training` table is for training and the `abalone_validation` table is for validation\.

   ```
   CREATE TABLE abalone_training as 
   SELECT 
       *
   FROM
       abalone_dataset
   WHERE
       mod(id, 10) < 8;
   
   CREATE TABLE abalone_validation as 
   SELECT 
       *
   FROM
       abalone_dataset
   WHERE
       mod(id, 10) >= 8;
   ```

## Step 2: Create the machine learning model<a name="tutorial_linear_learner_regression_step_create_model"></a>

In this step, you use the CREATE MODEL statement to create your machine learning model with the linear learner algorithm\. 

The following query creates the linear learner model with the CREATE MODEL operation using your S3 bucket\. Replace *`DOC-EXAMPLE-BUCKET`* with your own S3 bucket\.

```
CREATE MODEL model_abalone_ring_prediction
FROM
    (
        SELECT
            Sex,
            Length,
            Diameter,
            Height,
            Whole,
            Shucked,
            Viscera,
            Shell,
            Rings AS target_label
        FROM
            abalone_training
    ) TARGET target_label FUNCTION f_abalone_ring_prediction IAM_ROLE default MODEL_TYPE LINEAR_LEARNER PROBLEM_TYPE REGRESSION OBJECTIVE 'MSE' SETTINGS (
        S3_BUCKET 'DOC-EXAMPLE-BUCKET',
        MAX_RUNTIME 15000
    );
```

### Show the status of model training \(optional\)<a name="tutorial_linear_learner_regression_show_status"></a>

You can use the SHOW MODEL command to know when your model is ready\.

Use the following query to monitor the progress of the model training\.

```
SHOW MODEL model_abalone_ring_prediction;
```

When the model is ready, the output of the previous operation should look similar to the following example\. Note that the output provides the `validation:mse` metric, which is the mean square error\. You will use the mean square error to validate the accuracy of the model in the next step\.

```
+--------------------------+----------------------------------------------------------------------------------------------------+
|        Model Name        |                                   model_abalone_ring_prediction                                    |
+--------------------------+----------------------------------------------------------------------------------------------------+
| Schema Name              | public                                                                                             |
| Owner                    | awsuser                                                                                            |
| Creation Time            | Thu, 30.06.2022 18:00:10                                                                           |
| Model State              | READY                                                                                              |
| validation:mse           |                                                                                           4.168633 |
| Estimated Cost           |                                                                                           4.291608 |
|                          |                                                                                                    |
| TRAINING DATA:           |                                                                                                    |
| Query                    | SELECT SEX , LENGTH , DIAMETER , HEIGHT , WHOLE , SHUCKED , VISCERA , SHELL, RINGS AS TARGET_LABEL |
|                          | FROM ABALONE_TRAINING                                                                              |
| Target Column            | TARGET_LABEL                                                                                       |
|                          |                                                                                                    |
| PARAMETERS:              |                                                                                                    |
| Model Type               | linear_learner                                                                                     |
| Problem Type             | Regression                                                                                         |
| Objective                | MSE                                                                                                |
| AutoML Job Name          | redshiftml-20220630180010947843                                                                    |
| Function Name            | f_abalone_ring_prediction                                                                          |
| Function Parameters      | sex length diameter height whole shucked viscera shell                                             |
| Function Parameter Types | bpchar float8 float8 float8 float8 float8 float8 float8                                            |
| IAM Role                 | default-aws-iam-role                                                                               |
| S3 Bucket                | DOC-EXAMPLE-BUCKET                                                                                 |
| Max Runtime              |                                                                                              15000 |
+--------------------------+----------------------------------------------------------------------------------------------------+
```

## Step 3: Validate the model<a name="tutorial_linear_learner_regression_step_validate"></a>

1. The following prediction query validates the accuracy of the model on the `abalone_validation` dataset by calculating mean square error and root mean square error\.

   ```
   SELECT
       ROUND(AVG(POWER((tgt_label - predicted), 2)), 2) mse,
       ROUND(SQRT(AVG(POWER((tgt_label - predicted), 2))), 2) rmse
   FROM
       (
           SELECT
               Sex,
               Length,
               Diameter,
               Height,
               Whole,
               Shucked,
               Viscera,
               Shell,
               Rings AS tgt_label,
               f_abalone_ring_prediction(
                   Sex,
                   Length,
                   Diameter,
                   Height,
                   Whole,
                   Shucked,
                   Viscera,
                   Shell
               ) AS predicted,
               CASE
                   WHEN tgt_label = predicted then 1
                   ELSE 0
               END AS match,
               CASE
                   WHEN tgt_label <> predicted then 1
                   ELSE 0
               END AS nonmatch
           FROM
               abalone_validation
       ) t1;
   ```

   The output of the previous query should look like the following example\. The value of the mean square error metric should be similar to the `validation:mse` metric shown by the SHOW MODEL operation’s output\.

   ```
   +-----+--------------------+
   | mse |        rmse        |
   +-----+--------------------+
   | 5.1 | 2.2600000000000002 |
   +-----+--------------------+
   ```

1. Use the following query to run the EXPLAIN\_MODEL operation on your prediction function\. The operation will return a model explainability report\. For more information about the EXPLAIN\_MODEL operation, see the [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html) in the Amazon Redshift Database Developer Guide\.

   ```
   SELECT
       EXPLAIN_MODEL ('model_abalone_ring_prediction');
   ```

   The following information is an example of the model explainability report produced by the previous EXPLAIN\_MODEL operation\. The values for each of the inputs are Shapley values\. The Shapley values represent the effect each input has on the prediction of your model, with higher\-valued inputs having more impact on the prediction\. In this example, the higher\-valued inputs have more impact on predicting the age of abalone\.

   ```
   { 
       "explanations": { 
           "kernel_shap": { 
               "label0": { 
                   "expected_value" :10.290688514709473,
                   "global_shap_values": { 
                       "diameter" :0.6856910187882492,
                       "height" :0.4415323937124035,
                       "length" :0.21507476107609084,
                       "sex" :0.448611774505744,
                       "shell" :1.70426496893776,
                       "shucked" :2.1181392924386994,
                       "viscera" :0.342220754059912,
                       "whole" :0.6711906974084011 
                   } 
               } 
           } 
       },
       "version" :"1.0" 
   };
   ```

1. Use the following query to calculate the percentage of correct predictions that the model makes about abalone that are not yet mature\. Abalone that are immature have 10 rings or less, and a correct prediction is accurate to within one ring of the actual number of rings\.

   ```
   SELECT
       TRUNC(
           SUM(
               CASE
                   WHEN ROUND(
                       f_abalone_ring_prediction(
                           Sex,
                           Length,
                           Diameter,
                           Height,
                           Whole,
                           Shucked,
                           Viscera,
                           Shell
                       ),
                       0
                   ) BETWEEN Rings - 1
                   AND Rings + 1 THEN 1
                   ELSE 0
               END
           ) / CAST(COUNT(SHELL) AS FLOAT),
           4
       ) AS prediction_pct
   FROM
       abalone_validation
   WHERE
       Rings <= 10;
   ```

## Related topics<a name="tutorial_linear_learner_regression_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL operation](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)