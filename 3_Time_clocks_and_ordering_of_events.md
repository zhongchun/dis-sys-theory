## Clocks and time in distributed systems:
Distributed systems often need to measure time, e.g.:

- Schedulers, timeouts, failure detectors, retry timers
- performance measurements, statistics, profiling
- Log files & databases: record when an event occurred
- Data with time-limited validity (e.g. cache entries)
- Determining order of events across several nodes

We distinguish two types of clock:

- **physical clocks**: count number of seconds elapsed
- **logical clocks**: count events, e.g. messages sent
## 3.1 Physical clocks
### Quarts clocks
#### What is quarts clocks

- Quartz crystal laser-trimmed to mechanically resonate at a specific frequency
- Piezoelectric effect: mechanical force ⇔ electric field
- Oscillator circuit produces signal at resonant frequency
- Count number of cycles to measure elapsed time
#### Quarts clock error: drift
Quartz clocks are cheap, but they are not totally accurate.
Temperature significantly affects drift.
### Atomic clocks

- Caesium-133 has a resonance (“hyperfine transition”) at ≈ 9 GHz
- Tune an electronic oscillator to that resonant frequency
- 1 second = 9,192,631,770 periods of that signal
- Accuracy ≈ 1 in 10−14 (1 second in 3 million years)
- Price ≈ £20,000 (?) (can get cheaper rubidium clocks for ≈ £1,000)
### GPS as time source
Based on atomic clocks (International Atomic Time, TAI).
### Coordinated Universal Time (UTC)
UTC is based on atomic time, but includes corrections to account for variations in the Earth's rotation.

- **Greenwich Mean Time** (GMT, solar time): it's noon when the sun is in the south, as seen from the Greenwich meridian
- **International Atomic Time** (TAI): 1 day is 24 × 60 × 60 × 9,192,631,770 periods of caesium-133’s resonant frequency
- **Problem**: speed of Earth's rotation is not constant
- **Compromise**: UTC is TAI with corrections to account for Earth rotation
- Time zones and daylight savings time are offsets to UTC

The difference between UTC and TAI is that UTC includes leap seconds, which are added as needed to keep UTC roughly in sync with the rotation of the Earth.
### Leap seconds
Every year, on 30 June and 31 December at 23:59:59 UTC, one of three things happens:

- The clock immediately jumps forward to 00:00:00, skipping one second (negative leap second)
- The clock moves to 00:00:00 after one second, as usual
- The clock moves to 23:59:60 after one second, and then moves to 00:00:00 after one further second (positive leap second)

Due to leap seconds, it is not true that an hour always as 3600 seconds. In the UTC timescale, a day can be 86,499 seconds, 86,400 seconds, or 86,401 seconds long due to a leap second. This complicates software that needs to work with dates and times.
### How computers represent timestamps
Two most common representations:

- **Unix time**: number of seconds since 1 January 1970 00:00:00 UTC (the "epoch"), not counting leap seconds
- **ISO 8601**: year, month, day, hour, minute, second, and timezon offset relative to UTC

example: 2022-11-09T09:50:17+00:00
### How most software deals with leap seconds
Simply ignore leap seconds, pretend that they don't exist, and hope that the problem somehow goes away. This approach is taken by Unix timestamps, and by the POSIX standard.
However, operating systems and distributeds systems often do rely on high-resolution timestamps for accurate measurements of time, where a difference of one second is very noticeable.
So we should take new measures.
## 3.2 Clock synchronisation and monotonic clocks
Atomic clocks are too expensive and bulky to build into every computer and phone, so quartz clocks are used. Since these clocks drift, they need adjustment from time to time, which is most commonly done using the Network Time Protocol (NTP). All mainstream operating systems have NTP clients built in.
### Clock synchronisation
Computer track physical time/UTC with a quartz clock. Due to **clock drift**, clock error gradually increases.

- Clock skew: difference between two clocks at a point in time
- Solution: Periodically get the current time from a server that has a more accurate time source (atomic clock or GPS receiver)
- Protocols: Network Time Protocol (**NTP**), Precison Time Protocol (**PTP**)
### Network Time Protocol (NTP)
Many operating system vendors run NTP servers, configure OS to use them by default.
Hierarchy of clock servers arranged into strata:

- Stratum 0: atomic clock or GPS receiver
- Stratum 1: synced directly with stratum 0 device
- Stratum 2: servers that sync with stratum 1, etc.

May contact multiple servers, discard outliers, average rest.
Makes multiple requests to the same server, use statistics to reduce random error due to variations in network latency.
Reduces clock skew to a few milliseconds in good network conditions, but can be much worse!
### Estimating time over a network
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669025951300-24a4f189-6ef1-404f-9e92-c2269f12fe12.png#averageHue=%23f7f7f7&clientId=u6e58a21c-3f5f-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=526&id=u1730d91c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1052&originWidth=1420&originalType=binary&ratio=1&rotation=0&showTitle=false&size=158038&status=done&style=none&taskId=u941a3039-4f60-4c4f-bb89-ef3eb06e673&title=&width=710)
### Correcting clock skew
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669025986525-8b61ab32-25f3-4c79-b890-00ab18d41eb8.png#averageHue=%23f4f4f4&clientId=u6e58a21c-3f5f-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=532&id=u837d898a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1064&originWidth=1428&originalType=binary&ratio=1&rotation=0&showTitle=false&size=236224&status=done&style=none&taskId=uf470f5d1-7fc4-4945-8f56-e5fc8125634&title=&width=714)
The fact that clocks may be stepped by NTP, i.e. suddenly moved forwards or backwards, has an important implication for any software that needs to measure elapsed time.
### Monotonic and time-of-day clocks
#### Time-of-day clock

- Time since a fixed date (e.g. 1 January 1970 epoch)
- May suddenly move forwards or backwards (NTP stepping), subject to leap second adjustments
- Timestamps can be compared across nodes (if synced)
- Java: System.currentTimeMillis()
- Linux: clock_gettime(CLOCK_REALTIME)
#### Monotonic clock

- Time since arbitrary point (e.g. when machine booted up)
- Always moves forwards at near-constant rate
- Good for measuring elapsed time on a single node
- Java: System.nanoTime()
- Linux: clock_gettime(CLOCK_MONOTONIC)
## 3.3 Causality and happends-before
### Orders of messages
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669027135913-e3931f25-73b8-4e0f-82fc-4d83036f253c.png#averageHue=%23f7f7f7&clientId=u6e58a21c-3f5f-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=524&id=ud5144365&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1048&originWidth=1408&originalType=binary&ratio=1&rotation=0&showTitle=false&size=157721&status=done&style=none&taskId=u220bf331-fb44-4c24-997d-248c5760030&title=&width=704)
### Physical timestamps inconsistent with causality
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669027172970-d38636c6-f399-4974-843d-9c5f22d3affc.png#averageHue=%23f6f6f6&clientId=u6e58a21c-3f5f-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=522&id=u7db8ef89&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1044&originWidth=1394&originalType=binary&ratio=1&rotation=0&showTitle=false&size=186891&status=done&style=none&taskId=ue67a69ef-be21-4ce4-b85a-3e02f8a7ce3&title=&width=697)
### The happens-before relation
An event is something happening at one node (sending or receiving a message, or a local execution step).
We say event a happens before event b (written a → b) if: 

- a and b occurred at the same node, and a occurred before b in that node’s local execution order; or
- event a is the sending of some message m, and event b is the receipt of that same message m (assuming sent messages are unique); or
- there exists an event c such that a→c and c→b.

The happens-before relation is a partial order: it is possible that neither a→b nor b→a. In that case, a and b are concurrent (written a || b).
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1349795/1669027317776-7da99862-e30d-4ddd-b63d-98ada4d5cc21.png#averageHue=%23f9f8f8&clientId=u6e58a21c-3f5f-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=529&id=u146fe01e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1058&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=151090&status=done&style=none&taskId=u541a04fb-4e2b-4570-8f09-b1fc944466f&title=&width=700)

