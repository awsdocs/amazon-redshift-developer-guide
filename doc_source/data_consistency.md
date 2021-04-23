# Managing data consistency<a name="data_consistency"></a>

Amazon Redshift provides transactional consistency on all producer and consumer clusters and shares up\-to\-date and consistent views of the data with all consumers\. 

You can continuously update data on the producer cluster\. All queries on a consumer cluster within a transaction read the same state of the shared data\. Amazon Redshift doesn't take into account the data that was changed by another transaction on the producer cluster that was committed after the beginning of the transaction on the consumer cluster\. After the data change is committed on the producer cluster, new transactions on the consumer cluster can immediately query the updated data\.

The strong consistency eliminates the risks of lower\-fidelity business reports that might contain invalid results during sharing of data\. This factor is especially important for financial analysis or where the results might be used to prepare datasets that are used to train machine learning models\.