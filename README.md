# Analysis-of-PIE-with-ECN-using-ns3
CN Course Project 

## Brief
PIE algorithm is defined in RFC 8033 where guidelines are provided to use ECN with
PIE. This project aims to understand the performance gain obtained by using ECN with PIE.<br>
Required experience: Knowledge of PIE and ECN<br>
Bonus experience: Knowledge of ns-3 is a plus.<br>
Difficulty: Low to Moderate<br>
Recommended Reading:
- (RFC 8033) Proportional Integral Controller Enhanced (PIE): A Lightweight Control
Scheme to Address the Bufferbloat Problem (Link: https://tools.ietf.org/html/rfc8033)
- PIE: A Lightweight Control Scheme to Address the Bufferbloat Problem (Link:
http://ieeexplore.ieee.org/document/6602305/)
- TCP and Explicit Congestion Notification (Link: https://dl.acm.org/citation.cfm?id=205512)
- ns-3 code for PIE (Path: ns-3.xx/src/traffic-control/model/pie-queue-disc.{h, cc})

### Team Members
- [Vichitr](https://github.com/vichitr) - 16CO152
- [Shashank Kumar](https://github.com/skumrao) - 16CO246
- [Rupesh Nitnaware](https://github.com/Iamrupesh) - 16CO243

### Instructors
Mohit P. Tahiliani , Saumya Hegde, Aurea Fernandes

### Mentors
Sikha Bakshi

## Work Done
- Modified PIE to support ECN.
- Modified PIE example to test ECN.
- Documentation of steps involved in the project.

## Result & Conclusion
Following is the output of pie-example without and with ECN support:-
### Without ECN support
```
*** PIE stats from Node 2 queue ***

Packets/Bytes received: 1023 / 973854
Packets/Bytes enqueued: 819 / 796916
Packets/Bytes dequeued: 819 / 796916
Packets/Bytes requeued: 0 / 0
Packets/Bytes dropped: 204 / 176938
Packets/Bytes dropped before enqueue: 204 / 176938
  Unforced drop: 204 / 176938
Packets/Bytes dropped after dequeue: 0 / 0
Packets/Bytes sent: 819 / 796916
Packets/Bytes marked: 0 / 0
```
### With ECN support
```
*** PIE stats from Node 2 queue ***

Packets/Bytes received: 910 / 847786
Packets/Bytes enqueued: 823 / 790804
Packets/Bytes dequeued: 823 / 790804
Packets/Bytes requeued: 0 / 0
Packets/Bytes dropped: 87 / 56982
Packets/Bytes dropped before enqueue: 87 / 56982
  Forced drop: 86 / 56930
  Unforced drop: 1 / 52
Packets/Bytes dropped after dequeue: 0 / 0
Packets/Bytes sent: 823 / 790804
Packets/Bytes marked: 319 / 301116
  Unforced mark: 319 / 301116
```
This shows that with ECN, performance of PIE increases significantly. 
<br><br>
    
## Wiki
Check out the [wiki](https://github.com/vichitr/Analysis-of-PIE-with-ECN-using-ns3/wiki)
<br>
- A brief introduction and key points on PIE and ECN.
- We have explained all the modifications done in PIE code.
- We have also given pseudo code for PIE and PIE with ECN.
- Refer to [Contents](https://github.com/vichitr/Analysis-of-PIE-with-ECN-using-ns3/wiki#contents) on [Wiki Home page](https://github.com/vichitr/Analysis-of-PIE-with-ECN-using-ns3/wiki)
