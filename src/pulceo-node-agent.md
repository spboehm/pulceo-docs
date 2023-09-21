# PULCEO Node Agent (PNA)

## Low-level Components

### iPerf3

PULCEO Node Agent (PNA) uses iPerf3 as low-level component for determining the available bandwidth between particular nodes.
In genernal, iPerf3 requires server and client to measure the currently available bandwidth for both TCP and UDP.

PNA invokes the server as follows: `/bin/iperf3 -s -p 5001 -f m` (Note: Port my be different) 
Now, the server start listening on port 5001 and waits for incoming requests by the client.

For TCP, PNA invokes the following command to start measuring by the client side: `/bin/iperf3 -c localhost -p 5001 -f m`. After successfull measuring the bandwidth (by default one measurement per second, 10 seconds in total), iPerf3 shows the following ouput:

On the server side:

```
-----------------------------------------------------------
Server listening on 5001 (test #1)
-----------------------------------------------------------
Accepted connection from ::1, port 54286
[  5] local ::1 port 5001 connected to ::1 port 54294
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  10.8 GBytes  92439 Mbits/sec                  
[  5]   1.00-2.00   sec  10.8 GBytes  92586 Mbits/sec                  
[  5]   2.00-3.00   sec  10.8 GBytes  92555 Mbits/sec                  
[  5]   3.00-4.00   sec  10.7 GBytes  92248 Mbits/sec                  
[  5]   4.00-5.00   sec  11.1 GBytes  95747 Mbits/sec                  
[  5]   5.00-6.00   sec  11.4 GBytes  97835 Mbits/sec                  
[  5]   6.00-7.00   sec  11.4 GBytes  97684 Mbits/sec                  
[  5]   7.00-8.00   sec  10.9 GBytes  93543 Mbits/sec                  
[  5]   8.00-9.00   sec  10.6 GBytes  90789 Mbits/sec                  
[  5]   9.00-10.00  sec  10.5 GBytes  90118 Mbits/sec                  
[  5]  10.00-10.00  sec   256 KBytes  38130 Mbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.00  sec   109 GBytes  93554 Mbits/sec                  receiver
```

On the client side:

```
Connecting to host localhost, port 5001
[  5] local ::1 port 54294 connected to ::1 port 5001
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  10.8 GBytes  92440 Mbits/sec    0   2.00 MBytes       
[  5]   1.00-2.00   sec  10.8 GBytes  92585 Mbits/sec    4   2.37 MBytes       
[  5]   2.00-3.00   sec  10.8 GBytes  92556 Mbits/sec    0   2.37 MBytes       
[  5]   3.00-4.00   sec  10.7 GBytes  92249 Mbits/sec    0   2.37 MBytes       
[  5]   4.00-5.00   sec  11.1 GBytes  95747 Mbits/sec    0   2.37 MBytes       
[  5]   5.00-6.00   sec  11.4 GBytes  97833 Mbits/sec    0   2.37 MBytes       
[  5]   6.00-7.00   sec  11.4 GBytes  97686 Mbits/sec    0   2.37 MBytes       
[  5]   7.00-8.00   sec  10.9 GBytes  93543 Mbits/sec    1   2.37 MBytes       
[  5]   8.00-9.00   sec  10.6 GBytes  90789 Mbits/sec    0   2.37 MBytes       
[  5]   9.00-10.00  sec  10.5 GBytes  90118 Mbits/sec    0   2.37 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   109 GBytes  93555 Mbits/sec    5             sender
[  5]   0.00-10.00  sec   109 GBytes  93554 Mbits/sec                  receiver

iperf Done.
```

The iPerf3 client side shows two rows as result, once tagged with sender and once tagged with receiver.
The row tagged with sender is the client-perceived throughput whereas the row tagged with receiver is the throughput perceived by the server.
In good network conditions, both rows are identical. However, they may differ if packet retransmission is required, as shown in the row of the sender 
In total, 5 packets were required to retransmit. This mitigates the perceived troughput on the receiver's side, because the receiver had to wait for packet retransmission by the client.
PNA includes both results in a scheduled measurement for transparency and fine-tuning purposes.
