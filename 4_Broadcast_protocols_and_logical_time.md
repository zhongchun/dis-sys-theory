## 4.1 Logical time
### Pyhsical timestamps inconsistent with causality
![4-1](./images/4-1.png)
### Physical clocks vs. Logical

- **Pysical clock**: count number of seconds elapsed
- **Logical clock**: count number of evnets occurred

Logical clocks are designed to **capture causal dependencies**.
### Lamport clocks algorithm
![4-2](./images/4-2.png)
![4-3](./images/4-3.png)
A Lamport timestamp is essentially an integer that counts the number of events that have occurred. As such, it has no direct relationship to pyhsical time.
![4-4](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669126703258-922c675b-7281-4cc0-9ef6-4ded4852b69f.png#averageHue=%23f6f6f6&clientId=u30973867-850b-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=456&id=u7ec88a0a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=912&originWidth=1218&originalType=binary&ratio=1&rotation=0&showTitle=false&size=158413&status=done&style=none&taskId=uc893c920-657a-4f58-be43-b67c71c0f13&title=&width=609)
Happens-before relation is a partial order.
Lamport timestamps is a total order. But it is in general not possible to tell whether those events are concurrent or whether one happened before the other. If we want to acheive these, we need a different type of logical time: a vector clock.
### Vector clocks
![4-5](./images/4-5.png)
![4-6](./images/4-6.png)
![4-7](./images/4-7.png)
![4-8.png](./images/4-8.png)
Lamport clocks provides a total order.
Vector clocks captures the partial order of happens-before.
## 4.2 Delivery order in broadcast protocols
### Broadcast protocols
Broadcast (multicast) in group communication:

- One node sends message, all nodes in group deliver it
- Set of group members may be fixed (static) or dynamic
- If one node is faulty, remaining group members carry on
- Note: concept is more general than IP multicast
### Receiving versus delivering
![4-9.png](./images/4-9.png)
### Forms of reliable broadcast
#### FIFO broadcast
If m1 and m2 are broadcast by the same node, and broadcast(m1) -> broadcast(m2), then m1 must be delivered before m2.
#### Causal broadcast
If broadcast(m1) -> broadcast(m2) then m1 must be delivered before m2.
#### Total order broadcast
If m1 is delivered before m2 on one node, then m1 must be delivered before m2 on all nodes.
#### FIFO-total order broadcast
Combination of FIFO broadcast and total order broadcast.
### FIFO broadcast
![4-10.png](./images/4-10.png)
### Causal broadcast
![4-11.png](./images/4-11.png)
### Total order broadcast
![4-12.png](./images/4-12.png)
![4-13.png](./images/4-13.png)
### Relationships between broadcast models
![4-14.png](./images/4-14.png)
## 4.3 Broadcast algorithms
### Broadcast algorithms
Break down into two layers:

- Make best-effort broadcast reliable by retransmitting dropped messages (retry + deduplicate)
- Enforce delivery order on top of reliable broadcast
### Eager reliable broadcast
![4-15.png](./images/4-15.png)
### Gossip protocols
![4-16.png](./images/4-16.png)
### FIFO broadcast algorithm
![4-17.png](./images/4-17.png)
### Causal broadcast algorithm
![4-18.png](./images/4-18.png)
### Total order broadcast algorithms
![4-19.png](./images/4-19.png)
