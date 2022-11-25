## 8.1 Collaboration and conflict resolution
Nowadays we use a lot of collaboration software:

- Examples: calendar sync, Google Docs, ...
- Several users/devices working on a shared file/document 
- Each user device has local replica of the data
- Update local replica anytime (even while offline), sync with others when network available 
- Challenge: how to reconcile concurrent updates？

Families of algorithms:

- Conflict-free Replicated Data Types (**CRDTs**)
   - Operation-based
   - State-based
- Operation Transformation (**OT**)
### Operation-based CRDTs
![8-1.png](./images/8-1.png)
![8-2.png](./images/8-2.png)
### State-based CRDTs
![8-3.png](./images/8-3.png)
![8-4.png](./images/8-4.png)
### Operational transformation
![8-5.png](./images/8-5.png)
## Text editing CRDT
![8-6.png](./images/8-6.png)
![8-7.png](./images/8-7.png)
![8-8.png](./images/8-8.png)
**Note**: The most wonderfull thing is the method `ELEMENTAT`.
## 8.2 Google' Spanner
![8-9.png](./images/8-9.png)
![8-10.png](./images/8-10.png)
![8-11.png](./images/8-11.png)
![8-12.png](./images/8-12.png)
## Summary

- Distributed systems are everywhere
- We use them every day
- Key goals: availability, scalability, performance
- Key problems: concurrency, faults, unbounded latency
- Key abstractions: replication, broadcast, consensus
- No one right way, just trade-offs
