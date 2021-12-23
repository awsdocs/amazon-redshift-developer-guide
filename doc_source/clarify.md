# Using model explainability with Amazon Redshift ML<a name="clarify"></a>

With model explainability in Amazon Redshift ML, you use feature importance values to help understand how each attribute in your training data contributes to the predicted result\. 

Model explainability helps improve your machine learning \(ML\) models by explaining the predictions that your models make\. Model explainability helps explain how these models make predictions using a feature attribution approach\. 

Amazon Redshift ML incorporates model explainability to provide model explanation functionality to Amazon Redshift ML users\. For more information about model explainability, see [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html) in the *Amazon SageMaker Developer Guide*\.

Model explainability also monitors the inferences that models make in production for feature attribution drift\. It also provides tools to help you generate model governance reports that you can use to inform risk and compliance teams, and external regulators\.

When you specify the AUTO ON or AUTO OFF option when using the CREATE MODEL statement, after the model training job finishes, SageMaker creates the explanation output\. You can use the EXPLAIN\_MODEL function to query the explainability report in a JSON format\. For more information, see [Machine learning functions](ml-function.md)\.