# PULCEO Node Agent (PNA)

## Low-level Components

### iPerf3

PULCEO Node Agent (PNA) uses iPerf3 as low-level component for determining the available bandwidth between particular nodes.
In genernal, iPerf3 requires server and client to measure the currently available bandwidth for both TCP and UDP.

PNA invokes the server as follows: `/bin/iperf3 -s -p 5001 -f m` (Note: Port my be different) 
Now, the server start listening on port 5001 and waits for incoming requests by the client.

#### TCP

For TCP, PNA invokes the following command to start measuring by the client side: `/bin/iperf3 -c localhost -p 5001 -b 0M -t 10 -f m`.
iPerf3 measures the bandwidth at unlimited bandwidth (`-b 0M`) with 10 measurements in total (`-t 10`).
After successfull measuring the bandwidth, iPerf3 shows the following ouput:

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

#### UDP

For UDP, PNA invokes the following command to start measuring by the client side: `/bin/iperf3 -c localhost -u -p 5001 -b 1M -t 1 -f m`. In UDP mode, iPerf3 targets a bitrate of 1 Mbit/sec, which is implicitly assumed in the already mentioned command. Alternating bitrates can be defined with the option `-b`, for example with `/bin/iperf3 -c localhost -u -p 5001 -b 5M -t 10 -f m`. After successfull measuring the bandwidth (by default one measurement per second, 10 seconds in total), iPerf3 shows the following ouput:

On the server side:

```
-----------------------------------------------------------
Server listening on 5001 (test #1)
-----------------------------------------------------------
Accepted connection from ::1, port 60254
[  5] local ::1 port 5001 connected to ::1 port 57466
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec   128 KBytes  1.05 Mbits/sec  0.017 ms  0/4 (0%)  
[  5]   1.00-2.00   sec   128 KBytes  1.05 Mbits/sec  0.029 ms  0/4 (0%)  
[  5]   2.00-3.00   sec   128 KBytes  1.05 Mbits/sec  0.024 ms  0/4 (0%)  
[  5]   3.00-4.00   sec   128 KBytes  1.05 Mbits/sec  0.024 ms  0/4 (0%)  
[  5]   4.00-5.00   sec   128 KBytes  1.05 Mbits/sec  0.023 ms  0/4 (0%)  
[  5]   5.00-6.00   sec  96.0 KBytes  0.79 Mbits/sec  0.028 ms  0/3 (0%)  
[  5]   6.00-7.00   sec   128 KBytes  1.05 Mbits/sec  0.029 ms  0/4 (0%)  
[  5]   7.00-8.00   sec   128 KBytes  1.05 Mbits/sec  0.025 ms  0/4 (0%)  
[  5]   8.00-9.00   sec   128 KBytes  1.05 Mbits/sec  0.028 ms  0/4 (0%)  
[  5]   9.00-10.00  sec   128 KBytes  1.05 Mbits/sec  0.025 ms  0/4 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-10.00  sec  1.22 MBytes  1.02 Mbits/sec  0.025 ms  0/39 (0%)  receiver
```

On the client side:

```
[  5] local ::1 port 57466 connected to ::1 port 5001
[ ID] Interval           Transfer     Bitrate         Total Datagrams
[  5]   0.00-1.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   1.00-2.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   2.00-3.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   3.00-4.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   4.00-5.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   5.00-6.00   sec  96.0 KBytes  0.79 Mbits/sec  3  
[  5]   6.00-7.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   7.00-8.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   8.00-9.00   sec   128 KBytes  1.05 Mbits/sec  4  
[  5]   9.00-10.00  sec   128 KBytes  1.05 Mbits/sec  4  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-10.00  sec  1.22 MBytes  1.02 Mbits/sec  0.000 ms  0/39 (0%)  sender
[  5]   0.00-10.00  sec  1.22 MBytes  1.02 Mbits/sec  0.025 ms  0/39 (0%)  receiver
```

The iPerf3 client side shows two rows as result, once tagged with sender and once tagged with receiver.
The row tagged with sender is the client-perceived throughput whereas the row tagged with receiver is the throughput perceived by the server.
In good network conditions, both rows are identical. However, they may differ packets get lost or arrive out-of-order.
In total 1.22 MBytes are transmitted from client to server with a bitrate of 1.02 Mbits/s.
Besides these measurements, iPerf3 also measures the Jitter, in this case 0.025 ms.
High Jitter values imply a temporal fluctuation in receiving packages.
Especially real-time applications can suffer from high Jitter values.
Similar to the TCP mode of iPerf3, PNA includes both result lines in a scheduled measurement for transparency and fine-tuning purposes.
