# ASSESSMENT WEEK 6

# Performance Evaluation and Analysis

## 1. Introduction 

This document outlines the performance testing methodology and results for the Assessment Week 2 deliverables. It integrates storage baselining (Week 11 Lab) and network throughput analysis (Week 11 Network Lab) to establish a comprehensive system performance profile. The objective is to identify resource bottlenecks and validate the system's ability to handle application loads securely

## Methodology: System Baseline

We will establish a control metrics for the system in an idle state to allow for accurate comparison during load testing. we will use tools like top, free and iostat. 


## Server Idle state 

### CPU and process state

img1 

### Memory Availabity 

img 2

### Disk Subsystem Idle state 

img 3

### Network 

img 4


## 2. Storage Subsystem Performance

we will run dd to find out disk speed limit. 

command :dd if=/dev/zero of=testfile bs=1M count=1024 oflag=direct

Monitoring: 

We will use the command : iostat -x 1



Observation: During the write test, iostat monitoring revealed:

%util: The percentage of time the device was busy reached 90.59%.

await: The average time (ms) for I/O requests to be served (specifically write requests) was 4.50 ms.

test 1 

test 2

## 3. Network Subsystem Performance

### Latency Testing

Tool Used: ping (ICMP)

Execution: Connectivity tested against the gateway/server to determine round-trip time (RTT).

```bash
command : # 10 packets to determine average stability
ping -c 10 192.168.10.4
```

img ping test

### Throughput Testing

Tool Used: iperf3 (TCP/UDP Benchmarking)

Execution:

Server Side: iperf3 -s

Client Side: iperf3 -c 192.168.10.4 -t 30

Observations: 

Observation: During the network throughput test, iperf3 monitoring revealed:

Bandwidth: The average transmission speed was 221 Mbits/sec.

Retransmission: The Retr column showed 0, indicating that no TCP packets were lost or needed to be resent during the test.

network_test img


### Web server concurrency Test

Installation : 

``` bash 
#installs apache server and the tools we will use for testing 

sudo apt install apache2 apache2-utils -y
```

img server-test

Observation:

Throughput (Requests per Second): 5658.61 [#/sec] 

Latency (Time per Request): 17.672 ms .

Transfer Rate: 60,481.87 Kbytes/sec (~60 MB/s).

## 4. Application Load Testing

### CPU and Memory Test 

Tool Used: Stress

code: 

``` bash
# CPU Stress (2 Cores)
stress --cpu 2 --timeout 60s

# Memory Stress (512MB allocation)
stress --vm 1 --vm-bytes 512M --timeout 60s

```

### CPU test 

cputest imag

Observation:

CPU: The stress process (PID 1435) saturated the processor, reaching 100% Core 0 usage.

Memory: RAM usage remained stable and low at 208MB (~2%).

### Memory Stress test 

img memory test 

observation : 
Memory Usage: System memory usage spiked to 3.39GB (out of 9.96GB). This confirms the tool successfully allocated the requested resources (approx. 2GB for the test + OS overhead).

NOTE : a spike in CPU usages can be seen this is due to CPU working hard to allocate and free the memory address repeatedly.

###  Performance Comparison Table (Baseline vs. Load)

| Metric | Baseline (Idle) | Under Stress (Load) | Change / Observation |
| :--- | :--- | :--- | :--- |
| **CPU Usage** | `4.2%` (System) | `100%` (Core Saturation) | **Significant Spike.** Stress test successfully bottlenecked the specific core. |
| **Memory Usage** | `446 MB` (~4.3%) | `3.39 GB` (~34%) | **+2.9 GB Increase.** System handled the spike without using Swap. |
| **Disk Throughput** | `0 MB/s` | `188.82 MB/s` (Write) | **High Throughput.** Sustained high write speeds during the `dd` test. |
| **Disk Saturation** | `0.00%` | `90.59%` | **Near Saturation.** The disk was pushed to its throughput limit. |
| **Disk Latency (await)**| `0 ms` | `4.50 ms` | **Low Latency.** Response time remained excellent (<5ms) despite high traffic. |
| **Network Speed** | `0 Mbps` | `221 Mbps` | **Stable.** Consistent bandwidth with **0** retransmissions. |


## 5. Bottleneck Identification 

Evidence: During the disk write test, the device saturation (%util) reached 90.59%, and the write throughput capped at 188.82 MB/s.

Impact: The storage system is the limiting factor for write-heavy operations. Any application requiring write speeds faster than ~190 MB/s will experience queuing and delays.

## Bottleneck mitigation strategies

1. Upgrade Storage Hardware: Replace the current drive with a high-performance NVMe SSD to increase write throughput beyond 188 MB/s.

2. Implement Caching: Configure a write-back cache (using the abundant free RAM) to absorb write spikes before they hit the disk.

3. I/O Distribution: If possible, separate the operating system and application logs onto different physical disks to reduce contention.






