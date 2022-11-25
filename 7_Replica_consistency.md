### Consistency
A word that means many different things in different contexts.

- ACID ([atomicity](https://en.wikipedia.org/wiki/Atomicity_(database_systems)), [consistency](https://en.wikipedia.org/wiki/Consistency_(database_systems)), [isolation](https://en.wikipedia.org/wiki/Isolation_(database_systems)), [durability](https://en.wikipedia.org/wiki/Durability_(database_systems))): a transaction transforms the database from one consistent state to another

Here, consistent = satisfying application-specific 

- Read-after-write consistency
- Replication: replica should be consistent with other replicas
- Consistency model
## 7.1 Two-phase commit
A distributed transaction: transaction that reads or writes data on multiple nodes. The data on these nodes may be replicas of the same dataset, or different parts of a larger dataset; a distributed transation applies in both cases.
### Distributed transactions
Recall atomicity in the context of ACID transactions:

- A transaction either commits or aborts
- If it commits, its updates are durable
- If it aborts, it has no visible side-effects
- ACID consistency (preserving invariants) relies on atomicity

If the transaction updates data on multiple nodes, this implies:

- Either all node must commit, or all must abort
- If any node crashes, all must abort
### Atomic commit versus consensus
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669361077970-0a33a271-54ef-4067-8cc2-274daa6f5048.png#averageHue=%23efeff5&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=457&id=uc54ba151&margin=%5Bobject%20Object%5D&name=image.png&originHeight=914&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&size=161898&status=done&style=none&taskId=ud50585ea-02f0-4ff0-86b5-8add70abb70&title=&width=604)
### Two-phase commit (2PC)
The most common algorithm to ensure atomic commitment across multiple nodes is the two-phase commit (2PC) protocol. More see paper: Jim N. Gray. Notes on data base operating systems. In R. Bayer, R.M. Graham, and G. Seegmuller, editors, Op- erating Systems, volume 60 of LNCS, pages 393–481. Springer, 1978. doi:10.1007/3-540-08755-9 9. URL http: //jimgray.azurewebsites.net/papers/dbos.pdf.
The communication flow of 2PC is illustrated as follow:
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669361208856-7e3cd2e0-3328-483a-b882-fc8d63d91a7d.png#averageHue=%23f6f6f6&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=u55db4732&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=137876&status=done&style=none&taskId=u567055e8-2b6c-4697-a905-750c886665c&title=&width=606)
And this is a coordinator-based 2PC. But there is a problem: a single point of failure.
### The coordinator in two-phase commit
What if the coordinator crashes?

- Coordinator writes its decision to disk
- When it recovers, read decision from disk and send it to replicas (or abort if no decision was made before crash)
- **Problem**: if coordinator crashes after preprare, but before broadcasting decision, other nodes do not know how it has decided
- Replicas participating in transaciton cannot commit or abort after responding "ok" to the prepare request (otherwise we risk violating atomicity)
- Algorithm is blocked until coordinator recovers

In order to avoid a single point of coordinator failure, proposed **a 2PC based on Paxos Commit**, see paper Jim Gray and Leslie Lamport. Consensus on transaction commit. ACM Transactions on Database Systems, 31(1):133–160, March 2006. doi:10.1145/1132863.1132867. URL http://db.cs.berkeley.edu/cs286/papers/paxoscommit-tods2006.pdf.
### Fault-tolerant two-phase commit
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669361731964-85ad03c4-26ec-499f-bffe-0ff327c990c7.png#averageHue=%23f5f5f5&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=u798223d6&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=207157&status=done&style=none&taskId=u8aa3668f-27a3-4aff-9373-00937664faa&title=&width=605)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669361740584-44c34fa8-7ec4-4834-92e7-7b469261a2b3.png#averageHue=%23f8f8f8&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=uca187332&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=154657&status=done&style=none&taskId=u7b9b8668-3995-4e34-9859-b4f14b1cabe&title=&width=605)
## 7.2 Linearizability
An atomic commitment protocol is a way of preserving consistency across multiple replicas in the face of faults, by ensuring that all participants of a transaction either commit or abort. However, when there are multiple nodes concurrently reading and modifying some shared data concurrently, ensuring the same commit or abort outcome for all nodes is not sufficient.
Multiple nodes concurrently accessing replicated data.
How do we define "consistency" here?
The strongest option: **linerarizability**

- Informally: every operation takes effect atomically sometime after it started and before it finished
- All operations behave as if executed on a single copy of the data (even if there are in fact multiple replicas)
- Consequence: every operation returns an "up-to-date" value, a.k.a. "strong consistency"
- Not just in distributed systems, also in shared-memory concurrency (memory on multi-core CPUs is not linearizable by default)

NOTE: linearizability != serializability

- Serializability: transactions have the same effect as if they had been executed in some serial order, but it does not define what that order should be
- Linearizability: defines the values that operations must return, depending on the concurrency and relavie order of those operations.

The purpose of linearizability is to make a system behave as if there was only a single copy of the data, and all operations on it happen atomically, even if the system is in fact replicated.
### ABD algorithm
ABD algorithm named after its authors Attiya, Bar-Noy,  and Dolev. More see: Hagit Attiya, Amotz Bar-Noy, and Danny Dolev. Sharing memory robustly in message-passing systems. Journal of the ACM, 42(1):124–142, January 1995. doi:10.1145/200836.200869. URL http://www.cse.huji.ac.il/course/2004/dist/p124- attiya.pdf.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669364946248-6e17d747-a371-43ea-aee6-a194ea83fc97.png#averageHue=%23f4f3f3&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=454&id=ubda2bcf1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=908&originWidth=1216&originalType=binary&ratio=1&rotation=0&showTitle=false&size=161943&status=done&style=none&taskId=ue9a9dcbb-5a69-43bb-a46b-153bb8fe436&title=&width=608)
### Linearizability for different types of operation
This ensures linearizability of get (quorum read) and set (blind write to quorum)

- When an operation finishes, the value read/written is stored on a quorum of replicas
- Every subsequent quorum operation will see that value
- Multiple concurrent writes may overwirte each othe

The set operation for which the ABD algorithm ensures linearizability is a so-called blind write (unconditional write): it simply overwrites the value of a data item, regardless of its previous value. If multiple clients concurrently write to the same item,  it uses a last-wirter-wins conflict resolution policy, i.e. one of those writes will end up as the winner and the other vlaues will silently be discard.
But in some applications, we want to be more carefull and overwirte a value only if it has not been concurrently modified by another node. This can be achieved with an atomic compare-and-swap (CAS) operation.
### CAS algorithm
It is possible to implement a linearizable, replicated CAS operation using total order broadcast.
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669365577413-45278124-396b-4c64-b327-0b668825165f.png#averageHue=%23f6f6f6&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=uad679afc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&size=191649&status=done&style=none&taskId=u129be4ff-a21d-4bcc-96de-cd816d39c8d&title=&width=604)
## 7.3 Eventual consistency
Linearizability is a very convenient consistency model for distributed systems, because it guarantees that a system behaves as if there was only one copy of the data, even if it is in fact replicated.
But this strong guarantee also comes at cost and therefore linearizability is not suitable for all applications.
Linearizability advantanges:

- Make a distributed system behave as if it were non-distributed
- Simple for applications to use

Downsides:

- **Performance** cost: lots of messages and waiting for responses
- **Scalability** limits: leader can be a bottleneck
- **Availability** problems: if you can't contact a quorum of nodes, you can't process any operations

**Eventual consistency**: a weaker model than linearizability. Different trade-off choices.
### The CAP (consistency, availability, partition tolerance) theorem
The trade-off is known as the CAP theorem, which states that if there is a network partition in a system, we must choose between one of the following options:

1. We can have linearizable consistency, but in this case, some relicas will not be able to respond to requests because they cannot communicate with a quorum. Not being able to respond to requests makes those nodes effectively unavailable.
2. We can allow replicas to respond to requests even if they cannot communicate with other replicas. In this case, they continue to be available, but we cannot guarantee linearizability.

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669366519700-7c40430b-c459-40c8-976b-2af7a48825a8.png#averageHue=%23f6f5f5&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=u6a3e18d4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=910&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=151099&status=done&style=none&taskId=uc7cb0e7e-fba8-47b8-ba4e-f3b68a9c6c5&title=&width=605)
### Eventual consistency
#### Eventual consistency
Replicas process operations based only on their local state.
If there are no more updates, eventually all replicas will be in the same state. (No guarantees how long it might take.)
#### Strong eventual consistency

- Eventual delivery: every update made to one non-faulty replica is eventually processed by every non-faulty replica。
- Convergence: any two replicas that have processed the same set of udpates are in the same state (even if updates were processed in a different order).

Properties:

- Does not require waiting for network communication
- Causal broadcast (or weaker) can disseminate updates
- Concurrent updates => conflicts need to be resolved

In both eventual consistency and strong eventual consistency, there is the possibility of different nodes concurrently updating the same object, leading to conflicts.
### Summary
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669366975806-4de73e77-3e1d-482f-9b4d-6d95153c0ce7.png#averageHue=%23f6f6f6&clientId=udda15ab2-4957-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=463&id=u6ae8fa3d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=926&originWidth=1222&originalType=binary&ratio=1&rotation=0&showTitle=false&size=178572&status=done&style=none&taskId=u801b1c52-5198-4e1c-88a1-47d0138f985&title=&width=611)
