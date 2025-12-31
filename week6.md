# Assessment Week 6

## Introduction 

The objective of this week is to  evaluate and optimize system performance by analyzing operating system behavior under diverse stress scenarios. Through rigorous testing of core metrics such as latency, throughput, and resource utilization, we isolate performance bottlenecks in selected applications. The findings presented herein detail the transition from baseline measurements to optimization, providing tangible evidence of efficiency gains through structured data logs and visual analytics


## Testing System Resources 

### 1. CPU 

Command given : stress-ng --cpu 2 --timeout 60s
expected output : Spike in CPU cores. 


![](/Images/stress-ng-cpu.png)

![](/Images/cpu_spike.png)

As expected two cores in our system were at their peak limit. 


### 2. RAM 

Commmand Given : stress-ng --vm 1 --vm-bytes 1G --timeout 120s

This command allocates more memory and uses multiple workers.
Expectation: 

RAM usage will rise, available memory will shrink, swap may activate, and overall system performance may temporarily degrade, which is exactly the expected outcome of a memory stress test.

![](/Images/RAM-testing.png)


![](/Images/RAM.png)


### 3. Disk I/O test 

Command Given : dd if=/dev/zero/ of=disk_testfile bs=1M count=1024 oflag=direct

This command creates a heavy write activity on the disk.

![](/Images/iostat_spike.png)


### 4. Network test 


1. Server set up 

we need to set up firewall first 

``` bash 


sudo ufw allow 6000

sudo iperf3 -s -p 6000

```

2. Workstation setup

We need to set up our workstation in order to test our network.

``` bash 
sudo iperf3 -c 10.0.0.3 -p 6000

```


Outcome: 


![](/Images/network_test.png)


## Optimization : Strategic Memory Management (Swappiness)

### The Bottleneck
By default, Linux utilizes a `vm.swappiness` value of **60**. This setting is somewhat legacy; it instructs the kernel to be "eager" about moving inactive data from ultra-fast RAM to the much slower hard disk (Swap).

### The Insight (Desk vs. Archive Analogy)
Imagine your RAM is your **physical desktop** and the Swap partition is a **filing cabinet** across the room.

* **At 60 (Default):** The OS acts like an anxious worker. Even if the desk is half-empty, it constantly stops working to walk across the room and file papers away. This walking time (Disk I/O) creates lag.
* **At 10 (Optimized):** We tell the OS: "Do not leave your desk until it is absolutely full." This keeps active data within arm's reach (RAM), significantly reducing system latency.

### The Fix
We lowered the threshold to **10**, forcing the operating system to maximize physical memory usage before resorting to disk paging.

### Implementation
```bash
# Apply immediate change to kernel runtime parameters
sudo sysctl vm.swappiness=10


