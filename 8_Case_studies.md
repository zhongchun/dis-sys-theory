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
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381177018-92d5ba64-8ffc-4d11-90df-d628b298160c.png#averageHue=%23f6f6f6&clientId=u1d6cd1f8-2de6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=ub0f33ca9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=179013&status=done&style=none&taskId=u5cb2cdf1-6c9d-4cb8-a7c6-b3b7cd5bc8f&title=&width=606)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381211224-fa22e2e3-3a08-4321-81ab-173799185077.png#averageHue=%23f1f1f1&clientId=u1d6cd1f8-2de6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=459&id=uae826d22&margin=%5Bobject%20Object%5D&name=image.png&originHeight=918&originWidth=1210&originalType=binary&ratio=1&rotation=0&showTitle=false&size=230322&status=done&style=none&taskId=u736399b8-69e3-4724-a05f-717a46c7f5a&title=&width=605)
### State-based CRDTs
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381311585-bfb0d530-1f9a-43b6-9927-37b2bc8fcf2e.png#averageHue=%23f6f6f6&clientId=u1d6cd1f8-2de6-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=457&id=u75965644&margin=%5Bobject%20Object%5D&name=image.png&originHeight=914&originWidth=1222&originalType=binary&ratio=1&rotation=0&showTitle=false&size=181049&status=done&style=none&taskId=ubd4c2418-c650-44c5-8834-da3732a10ab&title=&width=611)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381407960-c048db04-25f0-4025-a112-0478b5d219d1.png#averageHue=%23f5f5f5&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=u3276ead9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1228&originalType=binary&ratio=1&rotation=0&showTitle=false&size=170739&status=done&style=none&taskId=u6d704d81-eb5d-481c-b6e7-daae6ca880a&title=&width=614)
### Operational transformation
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381477934-7102be3d-5fe2-4f91-a254-aef5515e14f6.png#averageHue=%23f7f7f7&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=459&id=uc8107fcd&margin=%5Bobject%20Object%5D&name=image.png&originHeight=918&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=122600&status=done&style=none&taskId=u52b521c1-af07-4698-945b-1d9f023e862&title=&width=610)
## Text editing CRDT
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381511933-c7779abe-f44c-4301-80bc-0380ae311f57.png#averageHue=%23f5f5f5&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=449&id=u8bd449e6&margin=%5Bobject%20Object%5D&name=image.png&originHeight=898&originWidth=1204&originalType=binary&ratio=1&rotation=0&showTitle=false&size=120680&status=done&style=none&taskId=ud94ac391-5100-4da7-8a4e-eb8f1248db6&title=&width=602)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381532888-2627f708-6825-4385-9b2f-a550f69e061b.png#averageHue=%23f5f5f5&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=uae073fdf&margin=%5Bobject%20Object%5D&name=image.png&originHeight=902&originWidth=1212&originalType=binary&ratio=1&rotation=0&showTitle=false&size=215206&status=done&style=none&taskId=u75c11312-9aba-42e8-9e9e-0b00bbf19bf&title=&width=606)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381542258-7694faad-5c78-47e6-bfcd-f2a1dc9d0a2e.png#averageHue=%23f5f5f5&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=450&id=u7cb501aa&margin=%5Bobject%20Object%5D&name=image.png&originHeight=900&originWidth=1196&originalType=binary&ratio=1&rotation=0&showTitle=false&size=180031&status=done&style=none&taskId=u1e739105-f646-4f85-b88b-87adbb03264&title=&width=598)
**Note**: The most wonderfull thing is the method `ELEMENTAT`.
## 8.2 Google' Spanner
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381610231-a6c56911-64ba-4660-9afb-11647289b340.png#averageHue=%23f2f2f2&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=452&id=u95ba0997&margin=%5Bobject%20Object%5D&name=image.png&originHeight=904&originWidth=1206&originalType=binary&ratio=1&rotation=0&showTitle=false&size=202400&status=done&style=none&taskId=ub1b7a420-932c-4b63-b05c-ce8d66b55d8&title=&width=603)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381621235-f0114380-12c5-4cf6-a9aa-400edb0123b6.png#averageHue=%23efefef&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=uf84d19ae&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1216&originalType=binary&ratio=1&rotation=0&showTitle=false&size=259984&status=done&style=none&taskId=uf5a7be08-d35b-4ace-80b7-96350bca362&title=&width=608)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381632538-58197835-d264-420d-b330-63da2aa220b3.png#averageHue=%23f6f5f5&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=453&id=ub103e9c3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=906&originWidth=1214&originalType=binary&ratio=1&rotation=0&showTitle=false&size=156756&status=done&style=none&taskId=u8236781b-d4b5-4045-b666-75c051a41c2&title=&width=607)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669381640657-ef286f93-f66e-4e3a-a222-d73b827362e6.png#averageHue=%23f8f7f7&clientId=ue9017171-cdbb-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=458&id=ud1d31fc9&margin=%5Bobject%20Object%5D&name=image.png&originHeight=916&originWidth=1220&originalType=binary&ratio=1&rotation=0&showTitle=false&size=154660&status=done&style=none&taskId=uf281dbe5-e5a2-4343-bce0-585f442c0e2&title=&width=610)
## Summary

- Distributed systems are everywhere
- We use them every day
- Key goals: availability, scalability, performance
- Key problems: concurrency, faults, unbounded latency
- Key abstractions: replication, broadcast, consensus
- No one right way, just trade-offs
