# STV\_WLM\_QMR\_CONFIG<a name="r_STV_WLM_QMR_CONFIG"></a>

Records the configuration for WLM query monitoring rules \(QMR\)\. For more information, see [WLM query monitoring rules](cm-c-wlm-query-monitoring-rules.md)\.

STV\_WLM\_QMR\_CONFIG is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STV_WLM_QMR_CONFIG-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QMR_CONFIG.html)

## Sample query<a name="r_STV_WLM_QMR_CONFIG-sample-query2"></a>

To view the QMR rule definitions for all service classes greater than 5 \(which includes user\-defined queues\), run the following query\. For a list of service class IDs, see [WLM service class IDs](cm-c-wlm-system-tables-and-views.md#wlm-service-class-ids)\.

```
Select *
from stv_wlm_qmr_config
where service_class > 5
order by service_class;
```