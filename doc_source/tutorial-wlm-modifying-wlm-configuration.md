# Section 2: Modifying the WLM query queue configuration<a name="tutorial-wlm-modifying-wlm-configuration"></a>

Now that you understand how queues work by default, you can learn how to configure query queues using manual WLM\. In this section, you create and configure a new parameter group for your cluster\. You create two additional user queues and configure them to accept queries based on the queries' user group or query group labels\. Any queries that don't get routed to one of these two queues are routed to the default queue at runtime\.

**To create a manual WLM configuration in a parameter group**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Configurations**, then choose **Workload management** to display the **Workload management** page\. 

1. Choose **Create** to display the **Create parameter group** window\. 

1. Enter **WLMTutorial** for both **Parameter group name** and **Description**, and then choose **Create** to create the parameter group\. 
**Note**  
The **Parameter group name** is converted to all lower case format when created\. 

1. On the **Workload management** page, choose the parameter group **wlmtutorial** to display the details page with tabs for **Parameters** and **Workload management**\. 

1. Confirm that you're on the **Workload management** tab, then choose **Switch WLM mode** to display the **Concurrency settings** window\. 

1. Choose **Manual WLM**, then choose **Save** to switch to manual WLM\. 

1. Choose **Edit workload queues**\. 

1. Choose **Add queue** twice to add two queues\. Now there are three queues: **Queue 1**, **Queue 2**, and **Default queue**\. 

1. Enter information for each queue as follows: 
   + For **Queue 1**, enter **30** for **Memory \(%\)**, **2** for **Concurrency on main**, and **test** for **Query groups**\. Leave the other settings with their default values\.
   + For **Queue 2**, enter **40** for **Memory \(%\)**, **3** for **Concurrency on main**, and **admin** for **User groups**\. Leave the other settings with their default values\.
   + Don't make any changes to the **Default queue**\. WLM assigns unallocated memory to the default queue\. 

1. Choose **Save** to save your settings\. 

Next, associate the parameter group that has the manual WLM configuration with a cluster\.

**To associate a parameter group with a manual WLM configuration with a cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.

1. On the navigation menu, choose **Clusters**, then choose **Clusters** to display a list of your clusters\. 

1. Choose your cluster, such as `examplecluster` to display the details of the cluster\. Then choose the **Properties** tab to display the properties of that cluster\. 

1. In the **Database configurations** section, choose **Edit**, **Edit parameter group** to display the parameter groups window\. 

1. For **Parameter groups** choose the **wlmtutorial** parameter group that you previously created\. 

1. Choose **Save changes** to associate the parameter group\. 

   The cluster is modified with the changed parameter group\. However, you need to reboot the cluster for the changes to also be applied to the database\.

1. Choose your cluster, and then choose **Reboot** for **Actions**\. 

After the cluster is rebooted, its status returns to **Available**\. 