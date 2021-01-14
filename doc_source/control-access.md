# Controlling shared data access<a name="control-access"></a>

As a producer cluster administrator, you retain control on the data sets you are sharing\. You can add new objects to or remove them from the data share\. You can also grant or revoke access to data shares as a whole for the consumer clusters\. When permissions are revoked, consumer clusters immediately lose access to the shared objects and stop seeing them as part of metadata queries\.

As a producer administrator, when you drop a data share, it stops being listed on consumer clusters\. The databases and schema references created on the consumer cluster from the dropped data share continues to exist with no objects in it\. The consumer cluster administrator needs to delete these databases manually\.

On the consumer side, consumer cluster administrator can determine which users and groups should access to the shared data and can control access at database and schema level\. If you want to further restrict access, you can create views on top of shared objects exposing only the necessary data\. You can then use these views to give access to the users and groups\.