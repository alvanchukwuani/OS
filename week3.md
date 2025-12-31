# Assessment week 3

## Introduction 

In this week, we will select some application to generate load to our server system resources. Application will be selected based on their speciality(CPU-intensive tool, Memory-intensive and so on)

## Application Selection Matrix

| Workload Type   | Tool(s) Used             | Why Chosen / How It Helps                          |
|-----------------|------------------------|---------------------------------------------------|
| CPU Stress      | stress-ng (CPU test)     | Pushes the processor hard to see how the system handles heavy computation. |
| Memory Stress   | stress-ng (Memory test)  | Fills up RAM to test how the system reacts under high memory demand. |
| Disk Activity   | dd (Disk test)           | Reads and writes large chunks of data to measure disk speed and reliability. |
| Network Load    | iperf3                   | Checks how fast data moves across the network, helping gauge bandwidth performance. |


## Expected Resource Profile

| Test / Tool               | Expected Resource Behavior                                                                 |
|----------------------------|-------------------------------------------------------------------------------------------|
| stress-ng (CPU Test)       | CPU usage will jump to 100% on each core being tested. Memory and disk I/O should stay low. |
| stress-ng (Memory Test)    | Memory usage will climb until available memory is very low. System may start using swap. CPU usage may be moderate. |
| dd (Disk Test)             | Disk I/O metrics will show high megabytes written per second. CPU may show high waiting for disk. |
| iperf3 (Network Test)      | Network traffic will max out the VM adapter's bandwidth. CPU usage will be moderate as it handles network packets. |


## Monitoring Strategy 


### For CPU-intesive

We will use stress-ng tool for generating CPU stress, we can do this by using this command : stress-ng --cpu 1 --timeout 60s

we can monitor the performance using htop. 


### For Memory intensive

We can use stress-ng to stress our memory by using this command : stress-ng --vm 1 --vm-bytes 4G --timeout 60s

We can use the same htop tool to monitor the performance.


### For IO-intensive

We can use the built in command line utlity called dd to test disk-IO, we can use this command : 

dd if=/dev/zero of=testfile bs=1M count=1024 oflag=direct

This writes 1 GB sequentially. 

We can use this command : iostat -x 2 to monitor the performance. 

### Network testing

We can utlize iperf3 tool to monitor the network performance. We need to install and set up iperf3 in both server and workstation to perform this test. 

on server : iperf3 -s -p 8000
On workstation : iperf3 -c <ip adress of server> -p 8000


