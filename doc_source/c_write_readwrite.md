# Write and read\-write operations<a name="c_write_readwrite"></a>

You can manage the specific behavior of concurrent write operations by deciding when and how to run different types of commands\. The following commands are relevant to this discussion: 
+ COPY commands, which perform loads \(initial or incremental\)
+ INSERT commands that append one or more rows at a time
+ UPDATE commands, which modify existing rows
+ DELETE commands, which remove rows 

COPY and INSERT operations are pure write operations, but DELETE and UPDATE operations are read\-write operations\. \(In order for rows to be deleted or updated, they have to be read first\.\) The results of concurrent write operations depend on the specific commands that are being run concurrently\. COPY and INSERT operations against the same table are held in a wait state until the lock is released, then they proceed as normal\.

UPDATE and DELETE operations behave differently because they rely on an initial table read before they do any writes\. Given that concurrent transactions are invisible to each other, both UPDATEs and DELETEs have to read a snapshot of the data from the last commit\. When the first UPDATE or DELETE releases its lock, the second UPDATE or DELETE needs to determine whether the data that it is going to work with is potentially stale\. It will not be stale, because the second transaction does not obtain its snapshot of data until after the first transaction has released its lock\.

## Potential deadlock situation for concurrent write transactions<a name="c_write_readwrite-potential-deadlock"></a>

Whenever transactions involve updates of more than one table, there is always the possibility of concurrently running transactions becoming deadlocked when they both try to write to the same set of tables\. A transaction releases all of its table locks at once when it either commits or rolls back; it does not relinquish locks one at a time\.

For example, suppose that transactions T1 and T2 start at roughly the same time\. If T1 starts writing to table A and T2 starts writing to table B, both transactions can proceed without conflict; however, if T1 finishes writing to table A and needs to start writing to table B, it will not be able to proceed because T2 still holds the lock on B\. Conversely, if T2 finishes writing to table B and needs to start writing to table A, it will not be able to proceed either because T1 still holds the lock on A\. Because neither transaction can release its locks until all its write operations are committed, neither transaction can proceed\.

In order to avoid this kind of deadlock, you need to schedule concurrent write operations carefully\. For example, you should always update tables in the same order in transactions and, if specifying locks, lock tables in the same order before you perform any DML operations\.