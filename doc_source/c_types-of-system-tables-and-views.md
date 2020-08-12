# Types of system tables and views<a name="c_types-of-system-tables-and-views"></a>

STL views are generated from logs that have been persisted to disk to provide a history of the system\. STV views are virtual views that contain snapshots of the current system data\. They are based on transient in\-memory data and are not persisted to disk\-based logs or regular tables\. System views that contain any reference to a transient STV table are called SVV views\. Views containing only references to STL views are called SVL views\.

System tables and views do not use the same consistency model as regular tables\. It is important to be aware of this issue when querying them, especially for STV tables and SVV views\. For example, given a regular table t1 with a column c1, you would expect that the following query to return no rows:

```
select * from t1
where c1 > (select max(c1) from t1)
```

However, the following query against a system table might well return rows:

```
select * from stv_exec_state
where currenttime > (select max(currenttime) from stv_exec_state)
```

 The reason this query might return rows is that currenttime is transient and the two references in the query might not return the same value when evaluated\.

On the other hand, the following query might well return no rows:

```
select * from stv_exec_state
where currenttime = (select max(currenttime) from stv_exec_state)
```