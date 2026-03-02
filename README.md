[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/aQEb8Lmc)

# COMP4651 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Saturday

---

### Name: LEUNG, Cheuk Nang

### Student Id: 20949965

### Email: cnleungaf@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

- `sysbench` was chosen to perform the CPU and memory performance benchmark
  - https://github.com/akopytov/sysbench

```
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | sudo bash
sudo apt -y install sysbench
```

```
echo "CPU"
sysbench cpu --threads=4 --cpu-max-prime=10000 run

echo "Memory Sequential Read"
sysbench memory --threads=4 --memory-oper=read --memory-access-mode=seq run

echo "Memory Random Read"
sysbench memory --threads=4 --memory-oper=read --memory-access-mode=rnd run

echo "Memory Sequential Write"
sysbench memory --threads=4 --memory-oper=write --memory-access-mode=seq run

echo "Memory Random Write"
sysbench memory --threads=4 --memory-oper=write --memory-access-mode=rnd run
```

**Parameter configuration:**

- `--threads=4`: Uses 4 threads for the stress test, to simulate multi-threaded workloads
- `--cpu-max-prime=10000`: Each CPU event computes all prime numbers up to 10,000. This is the suggested default value and would provide meaningful result in a reasonable time.
- `--memory-oper=read/write`: Selects the memory operation direction to measure both read and write bandwidth independently.
- `--memory-access-mode=seq/rnd`: Sequential mode accesses memory linearly (cache/prefetch friendly); random mode accesses random addresses (stresses the memory subsystem more heavily).

**What the values represent:**

- **CPU – events per second**: The number of prime-computation tasks (each computing all primes ≤ 10,000) completed per second across all threads. A higher value indicates better CPU throughput.
- **Memory – MiB/sec (transferred per second)**: The memory bandwidth achieved during the test. A higher value indicates faster data movement between CPU and RAM.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource

   In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

   | Size        | CPU performance (events/sec) | Memory performance (MiB/sec)                                                              |
   | ----------- | ---------------------------- | ----------------------------------------------------------------------------------------- |
   | `t2.micro`  | 2291.38                      | Seq Read: 525.14 <br> Rnd Read: 413.11 <br> Seq Write: 519.87 <br> Rnd Write: 413.58      |
   | `t2.medium` | 3825.33                      | Seq Read: 884.86 <br> Rnd Read: 723.31 <br> Seq Write: 850.36 <br> Rnd Write: 619.90      |
   | `c5d.large` | 1811.76                      | Seq Read: 11167.03 <br> Rnd Read: 2547.15 <br> Seq Write: 7660.91 <br> Rnd Write: 2528.38 |

   > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

<details>
  <summary>Appendix</summary>
  
1. t2.micro: 1 vCPU, 1 GiB memory

```
CPU
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)
                                                                                                  Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Prime numbers limit: 10000

Initializing worker threads...

Threads started!

CPU speed:
    events per second:  2291.38

General statistics:
    total time:                          10.0007s
    total number of events:              22920

Latency (ms):
         min:                                    0.33
         avg:                                    1.74
         max:                                   16.56
         95th percentile:                        5.37
         sum:                                39843.69

Threads fairness:
    events (avg/stddev):           5730.0000/27.74
    execution time (avg/stddev):   9.9609/0.01

Memory Sequential Read
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: read
  scope: global

Initializing worker threads...

Threads started!

Total operations: 5378839 (537740.78 per second)

5252.77 MiB transferred (525.14 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              5378839

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   16.74
         95th percentile:                        0.00
         sum:                                13226.54

Threads fairness:
    events (avg/stddev):           1344709.7500/7158.51
    execution time (avg/stddev):   3.3066/0.06

Memory Random Read
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: read
  scope: global

Initializing worker threads...

Threads started!

Total operations: 4230953 (423021.46 per second)

4131.79 MiB transferred (413.11 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              4230953

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   13.06
         95th percentile:                        0.00
         sum:                                19071.47

Threads fairness:
    events (avg/stddev):           1057738.2500/5947.18
    execution time (avg/stddev):   4.7679/0.15

Memory Sequential Write
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...

Threads started!

Total operations: 5324367 (532342.76 per second)

5199.58 MiB transferred (519.87 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              5324367

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   17.74
         95th percentile:                        0.00
         sum:                                13565.68

Threads fairness:
    events (avg/stddev):           1331091.7500/7328.39
    execution time (avg/stddev):   3.3914/0.04

Memory Random Write
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...

Threads started!

Total operations: 4235948 (423505.28 per second)

4136.67 MiB transferred (413.58 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              4235948

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   24.73
         95th percentile:                        0.00
         sum:                                18874.30

Threads fairness:
    events (avg/stddev):           1058987.0000/4032.52
    execution time (avg/stddev):   4.7186/0.04
```

2. t2.medium: 2 vCPU, 4 GiB memory

```
CPU
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Prime numbers limit: 10000

Initializing worker threads...

Threads started!

CPU speed:
    events per second:  3825.33

General statistics:
    total time:                          10.0007s
    total number of events:              38266

Latency (ms):
         min:                                    0.33
         avg:                                    1.04
         max:                                   10.07
         95th percentile:                        3.25
         sum:                                39873.73

Threads fairness:
    events (avg/stddev):           9566.5000/32.39
    execution time (avg/stddev):   9.9684/0.01

Memory Sequential Read
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:                                                 Number of threads: 4
Initializing random number generator from current time

Running memory speed test with the following options:                                      block size: 1KiB
  total size: 102400MiB                                                                    operation: read
  scope: global
Initializing worker threads...
Threads started!
Total operations: 9062767 (906094.32 per second)
8850.36 MiB transferred (884.86 MiB/sec)
                                                                                         General statistics:
    total time:                          10.0001s                                            total number of events:              9062767
                                                                                         Latency (ms):
         min:                                    0.00                                             avg:                                    0.00
         max:                                   11.31                                             95th percentile:                        0.00
         sum:                                13541.14
Threads fairness:                                                                            events (avg/stddev):           2265691.7500/7567.55
    execution time (avg/stddev):   3.3853/0.04
Memory Random Read                                                                       sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)
                                                                                         Running the test with following options:
Number of threads: 4                                                                     Initializing random number generator from current time

Running memory speed test with the following options:                                      block size: 1KiB
  total size: 102400MiB                                                                    operation: read
  scope: global
Initializing worker threads...
Threads started!

Total operations: 7408994 (740671.24 per second)
7235.35 MiB transferred (723.31 MiB/sec)
                                                                                         General statistics:
    total time:                          10.0001s
    total number of events:              7408994
                                                                                         Latency (ms):
         min:                                    0.00                                             avg:                                    0.00
         max:                                   11.23                                             95th percentile:                        0.00
         sum:                                18795.39
Threads fairness:
    events (avg/stddev):           1852248.5000/11699.37
    execution time (avg/stddev):   4.6988/0.02

Memory Sequential Write
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...
                                                                                         Threads started!
                                                                                         Total operations: 8709202 (870766.26 per second)
                                                                                         8505.08 MiB transferred (850.36 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              8709202

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                    8.33
         95th percentile:                        0.00
         sum:                                15019.11

Threads fairness:
    events (avg/stddev):           2177300.5000/11188.68
    execution time (avg/stddev):   3.7548/0.04

Memory Random Write
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...

Threads started!

Total operations: 6349479 (634779.26 per second)

6200.66 MiB transferred (619.90 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              6349479

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   12.30
         95th percentile:                        0.00
         sum:                                21185.85

Threads fairness:
    events (avg/stddev):           1587369.7500/15358.23
    execution time (avg/stddev):   5.2965/0.02
```

3. c5d.large: 2 vCPU, 4 GiB memory

```
CPU                                                                                      sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)
                                                                                         Running the test with following options:
Number of threads: 4                                                                     Initializing random number generator from current time


Prime numbers limit: 10000

Initializing worker threads...

Threads started!

CPU speed:
    events per second:  1811.76

General statistics:
    total time:                          10.0015s
    total number of events:              18123

Latency (ms):
         min:                                    1.09
         avg:                                    2.21
         max:                                    5.17
         95th percentile:                        3.13
         sum:                                39990.69

Threads fairness:
    events (avg/stddev):           4530.7500/25.14
    execution time (avg/stddev):   9.9977/0.00

Memory Sequential Read
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: read
  scope: global

Initializing worker threads...

Threads started!

Total operations: 104857600 (11435036.86 per second)

102400.00 MiB transferred (11167.03 MiB/sec)


General statistics:
    total time:                          9.1683s
    total number of events:              104857600

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                    4.12
         95th percentile:                        0.00
         sum:                                14178.98

Threads fairness:
    events (avg/stddev):           26214400.0000/0.00
    execution time (avg/stddev):   3.5447/0.06

Memory Random Read
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: read
  scope: global

Initializing worker threads...

Threads started!

Total operations: 26087043 (2608285.91 per second)

25475.63 MiB transferred (2547.15 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              26087043

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                    5.61
         95th percentile:                        0.00
         sum:                                34557.34

Threads fairness:
    events (avg/stddev):           6521760.7500/13010.80
    execution time (avg/stddev):   8.6393/0.02

Memory Sequential Write
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...

Threads started!

Total operations: 78460563 (7844767.18 per second)

76621.64 MiB transferred (7660.91 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              78460563

Latency (ms):                                                                                     min:                                    0.00
         avg:                                    0.00
         max:                                    4.08
         95th percentile:                        0.00                                             sum:                                23500.90
                                                                                         Threads fairness:
    events (avg/stddev):           19615140.7500/132236.71
    execution time (avg/stddev):   5.8752/0.04

Memory Random Write                                                                      sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Running memory speed test with the following options:
  block size: 1KiB
  total size: 102400MiB
  operation: write
  scope: global

Initializing worker threads...

Threads started!

Total operations: 25894723 (2589056.34 per second)

25287.82 MiB transferred (2528.38 MiB/sec)


General statistics:
    total time:                          10.0001s
    total number of events:              25894723

Latency (ms):
         min:                                    0.00
         avg:                                    0.00
         max:                                   10.44
         95th percentile:                        0.00
         sum:                                34566.44

Threads fairness:
    events (avg/stddev):           6473680.7500/47127.63
    execution time (avg/stddev):   8.6416/0.03
```

</details>

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

   | Type                      | TCP b/w (Mbps) | RTT (ms) (min/avg/max/mdev) |
   | ------------------------- | -------------- | --------------------------- |
   | `t3.medium` - `t3.medium` | 4850 Mbps      | 0.298/0.323/0.377/0.020 ms  |
   | `m5.large` - `m5.large`   | 4950 Mbps      | 0.236/0.252/0.278/0.010 ms  |
   | `c5n.large` - `c5n.large` | 4920 Mbps      | 0.130/0.134/0.152/0.006 ms  |
   | `t3.medium` - `c5n.large` | 4910 Mbps      | 0.449/0.474/0.602/0.044 ms  |
   | `m5.large` - `c5n.large`  | 4920 Mbps      | 0.504/0.519/0.599/0.027 ms  |
   | `m5.large` - `t3.medium`  | 4900 Mbps      | 0.876/0.896/0.913/0.009 ms  |

   > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

Installation

```
sudo apt update && sudo apt install -y iperf3 iputils-ping
```

Bandwidth benchmark

```
iperf3 -s
iperf3 -c <ip_address_of_server>
```

RTT Benchmark

```
ping -c 10 <ip_address_of_target>
```

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

   | Connection                | TCP b/w (Mbps) | RTT (ms) (min/avg/max/mdev)   |
   | ------------------------- | -------------- | ----------------------------- |
   | N. Virginia - Oregon      | 535 Mbps       | 54.775/54.794/54.831/0.013 ms |
   | N. Virginia - N. Virginia | 4770 Mbps      | 0.160/0.171/0.184/0.006 ms    |
   | Oregon - Oregon           | 4790 Mbps      | 0.174/0.184/0.205/0.008 ms    |

   > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.
