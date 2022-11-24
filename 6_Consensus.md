### Fault-tolerant total order broadcast
Total order broadcast is very usefull for enabling state machine replication. One way of implementing total order broadcast is by designating one node as the leader, and routing all messages via it. The leader then just needs to distributed the messages via FIFO broadcast, and this is sufficient to ensure that all nodes deliver the same sequence of messages in the same order.
But the leader is a single point of failure. What if leader crashes/becomes unavailable?

- Manual failover: a human operator chooses a new leader and reconfigures echo node to use new leader.

Used in many databases! Fine for planned manitenance. Unplanned outage? Humans are slow, may take a long time until system recovers...

- Automatically choose a new leader
## 6.1 Introduction to consensus
Consensus problem: several nodes want to come to agreement about a value. One or more nodes may propose a value, and then the consensus algorithm will decide on one of those values.
### Consensus and total order broadcast

- Traditional formulation of consensus: several nodes want to come to agreement about a single value
- In context of total order broadcast: this value is the next message to deliver
- Once one node decides on a certain message order, all nodes will decide the same order
- Consensus and total order broadcast are formally equivalent

Common consensus algorithms:

- Paxos: single-value consensus

Multi-Paxos: generalisation to total order broadcast

- Raft, Viewstamped Replication, Zab: FIFO-total order broadcast by default

It has been proved that no deterministic, asynchronous algorithm can solve the consensus problem with guaranteed termination. The fact is known as the FLP reulst, one of the most import theorems of distributed computing, named after its three authors Fischer, Lynch, and Paterson. Details in: Michael J. Fischer, Nancy A. Lynch, and Michael S. Paterson. Impossibility of distributed consensus with one faulty process. Journal of the ACM, 32(2):374–382, April 1985. doi:10.1145/3149.214121. URL https://groups.csail.mit.edu/tds/papers/ Lynch/jacm85.pdf.
### Leader election
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669300123690-d1ed682a-8236-4210-9c19-f0d3578e0065.png#averageHue=%23f3efef&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=u3d2f7455&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1198&originalType=binary&ratio=1&rotation=0&showTitle=false&size=236622&status=done&style=none&taskId=ubf714b7d-330b-49fa-9521-19d8278aec7&title=&width=599)
## 6.2 The Raft consensus algorithm
Raft is a protocol for implementing distributed consensus.
For a graphical visualisation of the algorithm, see [http://thesecretlivesofdata.com/raft/](http://thesecretlivesofdata.com/raft/)
The Raft Paper see [https://raft.github.io/raft.pdf](https://raft.github.io/raft.pdf)
Raft Web site: 

- Leader election
- Log Replication: the same Append Entries message

Every node has:

- a state machine
- a log

There are two timetous:

- election timeout: is randomized
- heartbeat timeout: is fixed; Appened Entries message
### Node state transitions in Raft
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669293039451-e487bb29-c167-4c17-a207-437d07f47075.png#averageHue=%23f7f7f7&clientId=uce0e053d-8282-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=u93e0fe91&margin=%5Bobject%20Object%5D&name=image.png&originHeight=910&originWidth=1206&originalType=binary&ratio=1&rotation=0&showTitle=false&size=128094&status=done&style=none&taskId=u0e217be7-ae2a-492b-9a58-3386397d65d&title=&width=603)
### Raft 1: initialisation
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299366448-2c1bec19-c214-4982-b2ba-cff6285103df.png#averageHue=%23f3f3f3&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=ua7ad7f73&margin=%5Bobject%20Object%5D&name=image.png&originHeight=910&originWidth=1218&originalType=binary&ratio=1&rotation=0&showTitle=false&size=253351&status=done&style=none&taskId=u2b42a5d5-710b-4436-b7b0-99e7075bffb&title=&width=609)
### Raft 2: voting on a new leader
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299401469-d08820ce-de33-45d4-8615-c9cf143f199d.png#averageHue=%23f5f5f5&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=454&id=uf6ef8689&margin=%5Bobject%20Object%5D&name=image.png&originHeight=908&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=224170&status=done&style=none&taskId=u64b07f6a-6cdd-4106-a3f6-8248854a016&title=&width=610)
### Raft 3: collecting votes
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299415017-363f31f1-8e14-4786-a85e-8d54f66a76ac.png#averageHue=%23f6f6f6&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=457&id=ua9206d7e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=914&originWidth=1206&originalType=binary&ratio=1&rotation=0&showTitle=false&size=207266&status=done&style=none&taskId=u67d00ef0-beb2-4e66-99bb-6af9c3eb12d&title=&width=603)
### Raft 4: broadcasting messages
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299451655-8e2a62a1-03e1-46bc-9714-4f2cd3307e81.png#averageHue=%23f6f6f6&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=u4676fc3f&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1200&originalType=binary&ratio=1&rotation=0&showTitle=false&size=187410&status=done&style=none&taskId=ua86937f1-008f-4d0c-94b3-86d9f7949c4&title=&width=600)
### Raft 5: replicating from leader to followers
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299464748-4f12fe23-7a70-4899-8f7f-781d127eb03f.png#averageHue=%23f5f5f5&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=450&id=u69346623&margin=%5Bobject%20Object%5D&name=image.png&originHeight=900&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=218467&status=done&style=none&taskId=u9da71fda-1674-462e-8896-f7aafff4e0d&title=&width=606)
### Raft 6: followers receiving messages
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299476726-dd20b317-687a-4fef-90e2-7fb61b992c63.png#averageHue=%23f4f4f4&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=u57111400&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1202&originalType=binary&ratio=1&rotation=0&showTitle=false&size=238007&status=done&style=none&taskId=u317f33b1-5b1c-418e-a16b-11eaf76dfc1&title=&width=601)
### Raft 7: updating follower's logs
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299488374-b9c5c569-9570-4a33-b058-3b2ecc18a724.png#averageHue=%23f5f5f5&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=u2adae2f8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=910&originWidth=1204&originalType=binary&ratio=1&rotation=0&showTitle=false&size=219057&status=done&style=none&taskId=u36d3daa2-6412-4301-b45c-1d5cc13f0ea&title=&width=602)
### Raft 8: leader receiving log acknowledgements
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299500690-5f75d268-def4-4e4a-b5d3-ecd91d4908aa.png#averageHue=%23f6f6f6&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=459&id=ube8d561e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=918&originWidth=1208&originalType=binary&ratio=1&rotation=0&showTitle=false&size=204982&status=done&style=none&taskId=uaa9f6de0-1997-4470-af5e-2f30c479fe7&title=&width=604)
### Raft 9: leader committing log entries
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669299512156-b7571ebd-9d60-4eb3-9e18-f4dcad69dfb6.png#averageHue=%23f5f5f5&clientId=uaaa7f8ca-1b5a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=u74527157&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=212458&status=done&style=none&taskId=u86cfbd1d-84f7-40ae-8c02-3d2b64e3b85&title=&width=606)
### Summary
Raft 1-3: leader election
Raft 4-9: process request
