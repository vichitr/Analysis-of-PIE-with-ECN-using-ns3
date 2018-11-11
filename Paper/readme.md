<h3>Buffer bloat</h3>

- Buffers are present to ensure smooth communication between a fast network and a slow network. 

- Act like shock absorbers and delay packets in the fast network so that the slow network can keep up with the packets. 

- Affects channel bandwidth.

- Excess buffers in the network cause high latency and latency variation

- Solved by various Active Queue Management algorithms.


References:

<ol>
	<li> https://tools.ietf.org/html/rfc8033 </li>
	<li> https://en.wikipedia.org/wiki/Bufferbloat </li>
</ol>

----------------------------------------------------------------------
<h3>ECN (Explicit Cingestion Notification)</h3>

- ECN allows end-to-end notification of network congestion without dropping packets.

- ECN reduces the number of packets dropped by a TCP connection, which, by avoiding a retransmission, reduces latency and especially jitter

- An ECN-aware router may set a mark in the IP header instead of dropping a packet in order to signal impending congestion.


<ul>
	<li>
	ECN uses the two least significant (right-most) bits of the DiffServ field in the IPv4 or IPv6 header to encode four different code points:
	<ul>
		<li>00 – Non ECN-Capable Transport, Non-ECT</li>
		<li>10 – ECN Capable Transport, ECT(0)</li>
		<li>01 – ECN Capable Transport, ECT(1)</li>
		<li>11 – Congestion Encountered, CE.</li>
	</ul>
	</li>
</ul>

References:
<ol>
	<li> https://en.wikipedia.org/wiki/Explicit_Congestion_Notification </li>
	<li> https://tools.ietf.org/html/rfc3168</li>
</ol>

----------------------------------------------------------------------
<h3>PIE (Proportional Integral Controller Enhanced)</h3>

- Similary to RED, drops packet randomly during enqueuing 

- Drops packets based on queuing latency instead of queue length

- Queue latency is calculated using queue length and dequeue rate

- Drop probability is reduced if queueing latency is below a threshold limit

## The Basic Pie Scheme

PIE is comprised of three simple basic components:

### Random dropping at enqueuing
PIE randomly drops a packet upon its arrival to a queue according to a drop probability, PIE->drop_prob_, that is obtained from the
drop-probability-calculation component.To ensure that PIE is "work conserving", we bypass the random drop if the latency sample, PIE->qdelay_old_, is smaller than half of the target latency value (QDELAY_REF) when the drop probability is not too high (i.e., PIE->drop_prob_ < 0.2), or if the queue has less than a couple of packets.

### Drop probability Calcultion
The PIE algorithm periodically updates the drop probability based on the latency samples -- not only the current latency sample but also whether the latency is trending up or down.When a congestion period ends, we might be left with a high drop probability with light packet arrivals.  Hence, the PIE algorithm includes a mechanism by which the drop probability decays exponentially (rather than linearly) when the system is not congested. This would help the drop probability converge to 0 more quickly, while the PI controller ensures that it would eventually reach zero.

### Latency calculation.
The PIE algorithm uses latency to calculate drop probability in one
   of two ways:

   -  It estimates the current queuing latency using Little's law (see
      Section 5.2 for details):

         current_qdelay = queue_.byte_length()/dequeue_rate;

   -  It may use other techniques for calculating queuing latency, e.g.,
      time-stamp the packets at enqueue, and use the timestamps to
      calculate latency during dequeue.

### Burst Tolerence
PIE allows bursts of traffic that create finite-duration events in which current queuing latency exceeds QDELAY_REF without triggering packet drops. The burst allowance, noted by PIE->burst_allowance_, is initialized to MAX_BURST. As long as PIE->burst_allowance_ is above zero, an incoming packet will be enqueued, bypassing the random drop process. During each update instance, the value of PIE->burst_allowance_ is decremented by the update period, T_UPDATE, and is bottomed at 0. When the congestion goes away -- defined here as PIE->drop_prob_   equals 0 and both the current and previous samples of estimated latency are less than half of QDELAY_REF -- PIE->burst_allowance_ is reset to MAX_BURST.

References:
<ol>
	<li> https://tools.ietf.org/html/rfc8033 </li>
	<li> https://tools.ietf.org/html/draft-ietf-aqm-pie-10 </li>
</ol>

----------------------------------------------------------------------
## Pie With ECN support

__________________________________________________________

## Flow Queue Logic

 Flow: A flow is typically identified by a 5-tuple of source IP, destination IP, source port, destination port, and protocol number. 

The intention of Flow Queue scheduler is to give each _flow_ its own queue.

### Enqueue

When a packet is en-queued, it is first classified into the appropriate queue.  By default, this is done by hashing (using a Jenkins hash function on the 5-tuple of IP protocol, and source and destination IP addresses and port numbers (if they exist) and taking the hash value modulo the number of queues.  

### Dequeue
It consists of three parts: 
- selecting a queue from which to dequeue a packet,
- actually dequeuing it (employing the appropriate algorithm (PIE/CoDel etc) algorithm in the process
 - final bookkeeping.

For the first part, the scheduler first looks at the list of new queues; for the queue at the head of that list, if that queue has a  negative number of credits (i.e., it has already dequeued at least a quantum of bytes), it is given an additional quantum of credits, the queue is put onto _the end of_ the list of old queues, and the routine selects the next queue and starts again.

Otherwise, that queue is selected for dequeue.  If the list of nee queues is empty, the scheduler proceeds down the list of old queues in the same fashion (checking the credits, and either selecting the queue for dequeuing, or adding credits and putting the queue back at the end of the list).

 After having selected a queue from which to dequeue a packet, the underlying algorithm is invoked on that queue.  As a result of this, one or more packets may be discarded from the head of the selected queue, before the packet that should be dequeued is returned (or nothing is returned if the queue is or becomes empty while being handled by the underlying algorithm).

Finally, if the underlying algorithm does not return a packet, then the queue must be empty, and the scheduler does one of two things: if the queue selected for dequeue came from the list of new queues, it is moved to _the end of_ the list of old queues.  If instead it came  from the list of old queues, that queue is removed from the list, to be added back (as a new queue) the next time a packet arrives that hashes to that queue.  Then (since no packet was available for  dequeue), the whole dequeue process is restarted from the beginning.

If, instead, the scheduler _did_ get a packet back from the underlying algorithm, it subtracts the size of the packet from the byte credits for the selected queue and returns the packet as the result of the dequeue operation.

________________________________________________________________________________________________________________________________________
