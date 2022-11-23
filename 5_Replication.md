Replication means to maintain a copy of the same data on mutiple nodes, each of which is called a replica.

- Keeping a copy of the same data on multiple nodes
- Databases, filesystems, caches, ...
- A node that has a copy of the data is called a **replica**
- If some replicas are faulty, others are still accessible
- Spread load across many replicas
- Easy if the data doesn't change: just copy it
- It will be complicated if data change

Comparation with RAID (Redundant Array of Independent Disks):

- RAID has single controller; in distributed system, each node acts independently
- Replicas can be distributed around the world, near users
## 5.1 Manipulating remote state
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669188208844-26cf86f1-0f5f-45e8-b9c6-b7ef577fbe7b.png#averageHue=%23f3f2f2&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=u6e5fc778&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=158356&status=done&style=none&taskId=ud82dd571-657d-4b44-895f-fcfdb855207&title=&width=606)
One way of preventing an update from taking effect multiple times is to deduplicate requests. However, in a crash-recovery system model, this requires storing requests (or some metadata about requests, such as a vector clock) in stable storage, so that duplicates can be accurately detected even after a crash.
An alternative to recording requests for deduplication is to make requests **idempotent**.
### Idempotence
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669188453209-98b349a2-36a8-49dd-be54-12ba49c6cfc3.png#averageHue=%23f4f4f4&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=u9dedd04f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=182591&status=done&style=none&taskId=ua8b06046-291c-4885-a863-3f9ec7bae58&title=&width=610)
An idempotent update can safely be retried, because performing it several times has the same effect as performing it once.
However, idempotence has a limitation that becomes apparent when there are multiple updates in progress.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669188761476-55958499-c6f8-465b-959e-bf4ca2479477.png#averageHue=%23f7f6f6&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=459&id=uea5be246&margin=%5Bobject%20Object%5D&name=image.png&originHeight=918&originWidth=1216&originalType=binary&ratio=1&rotation=0&showTitle=false&size=144137&status=done&style=none&taskId=u1b2ab7bd-1e19-47fe-83ea-eda19f86da3&title=&width=608)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669188802150-a6d29cfb-7889-463d-a90e-b63b37f46a7e.png#averageHue=%23f7f4f4&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=udb5459cf&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=152831&status=done&style=none&taskId=u1ab4426f-0e5e-4fee-83ac-17f04511e58&title=&width=606)
To solve this problem, we can do two things.

- attach a logical timestamp to every update operation, and store that timestamp in the database as part of the data written by the update
- don't actually remote the record when asked to remote a record from the database, but rather write a special type of update (called a tombstone) marking it as deleted

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669189011296-e1184dae-0abf-4612-8f8c-6c8463942ea9.png#averageHue=%23f5f4f4&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=457&id=u1c04b5da&margin=%5Bobject%20Object%5D&name=image.png&originHeight=914&originWidth=1222&originalType=binary&ratio=1&rotation=0&showTitle=false&size=170895&status=done&style=none&taskId=u4c813e50-769c-41bb-b334-848ae4af2cb&title=&width=611)
In many replicated systems, replicas run a protocol to detect and reconcile any differences (that is called anti-entropy), so that the replicas eventually hold consistent copies of the same data. Thanks to tombstones, the anti-entropy process can tell the difference between a record that has been deleted and a record that has not yet been created. And thanks to timestamps, we can tell which version of a record is older and which is newer. The anti-entropy process then keeps the newer and discards the older record.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669189242310-d231f804-85e2-475d-8c19-c3e7375d4630.png#averageHue=%23f6f6f6&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=460&id=u36461e2b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=920&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=149774&status=done&style=none&taskId=u64accae6-e6c1-4beb-bb3f-14c4af293cd&title=&width=610) 
### Concurrent wirtes by different clients
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669189309904-fbd656c9-4d6c-4539-9ef0-65c360c1d46a.png#averageHue=%23f4f3f3&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=u7b9d8a86&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&size=190985&status=done&style=none&taskId=ub5d4462d-c043-48e8-ad80-2b0f8fd164c&title=&width=604)
The details of this approach depend on the type of timestamps used.

- Lamport clocks capure a total order
- Vector clocks capure the partial order of happens-before
## 5.2 Quorum
A quorum is a minimum set of nodes that must respond to some request for it to be succeful.
### Probability of faults
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669189812133-63362d6b-bb99-42a3-ab3d-0345e630c54c.png#averageHue=%23f2f2f2&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=456&id=u6a708281&margin=%5Bobject%20Object%5D&name=image.png&originHeight=912&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=205682&status=done&style=none&taskId=u682b80b1-aba3-43ab-afc8-6076c11a6c0&title=&width=605)
### Read-after-write consistency
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669189848168-7a1c204d-fd1d-481b-8fb6-c08257d4aee4.png#averageHue=%23f6f5f5&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=463&id=u27d86d0e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=926&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=153775&status=done&style=none&taskId=u7722adc2-d4e3-4fcd-80fc-dbab1e5ae0c&title=&width=610)
We can solve this conundrum by using three replicas.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669190077746-2b28d939-f13a-4a27-b69e-1bb681c1a1a3.png#averageHue=%23f4f2f2&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=450&id=u7987c026&margin=%5Bobject%20Object%5D&name=image.png&originHeight=900&originWidth=1204&originalType=binary&ratio=1&rotation=0&showTitle=false&size=161872&status=done&style=none&taskId=u8e117415-130e-4f4c-b147-4052f90cec1&title=&width=602)
### Read and write quorums
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669190103341-7b8893ad-6067-4f40-ad3b-c06501d705de.png#averageHue=%23f4f2f2&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=456&id=u4e0e5ea1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=912&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=217878&status=done&style=none&taskId=u8b04623d-554f-4e1a-bca1-0bd559795f8&title=&width=605)
### Read repair
Another option is to ge clients to help with the process of disseminating updates.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669190164418-745d3635-0bca-4b9d-a4bc-939aa9a40bd2.png#averageHue=%23f6f5f5&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=454&id=u326b9ae0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=908&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=134404&status=done&style=none&taskId=u38f59c5c-0944-4523-9dd3-806f520fc3d&title=&width=610)
Databases that use this model fo replication are often called Dynamo-style, after Amazon's Dynamo database.
## 5.3 Replication using broadcast
Use FIFO-total order broadcast to implement replication.
### State machine replication
Total order broadcast: every node delivers the same messages in the same order.
**Stage machine replication** (SMR):

- FIFO-total order broadcast every update to all replicas
- Replica delivers update message: apply it to own state
- Applying an update is deterministic
- Replica is a state machine: starts in fixed initial state, goes through same sequence of state transitions in the same order => all replicas end up in the same state

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669212880801-abe68a1f-b27b-40d5-8539-742c7e120fc5.png#averageHue=%23f3f3f3&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=u6b3e57ba&margin=%5Bobject%20Object%5D&name=image.png&originHeight=910&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=189393&status=done&style=none&taskId=ue74f71ab-3f78-48ed-8122-73489c3aba3&title=&width=606)
The downsides of state machine replication are the limitations of total order broadcast.
When a node wants to broadcast a message through a total order broadcast, it cannot immediately deliver that message to itself.
### Replication using causal (and weaker) broadcast
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669213030786-6fc7096b-9ac3-4393-a062-f2309bf4bc88.png#averageHue=%23f1f1f1&clientId=u4d2814af-4928-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=456&id=ub2a6d7da&margin=%5Bobject%20Object%5D&name=image.png&originHeight=912&originWidth=1230&originalType=binary&ratio=1&rotation=0&showTitle=false&size=230391&status=done&style=none&taskId=ud9b3cf25-b2c3-4787-b3e4-b135d0ff850&title=&width=615)
