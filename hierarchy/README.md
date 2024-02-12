***1.Local Memory***

Χρησιμοποιώντας το εργαλείο mlc είχα τα ακόλουθα αποτελέσματα :

Intel(R) Memory Latency Checker - v3.11
Measuring idle latencies for random access (in ns)...
                Numa node
Numa node            0
       0          97.1

Measuring Peak Injection Memory Bandwidths for the system
Bandwidths are in MB/sec (1 MB/sec = 1,000,000 Bytes/sec)
Using all the threads from each core if Hyper-threading is enabled
Using traffic with the following read-write ratios
ALL Reads        :      34795.7
3:1 Reads-Writes :      24258.2
2:1 Reads-Writes :      22634.7
1:1 Reads-Writes :      20599.5
Stream-triad like:      25593.7

Measuring Memory Bandwidths between nodes within system
Bandwidths are in MB/sec (1 MB/sec = 1,000,000 Bytes/sec)
Using all the threads from each core if Hyper-threading is enabled
Using Read-only traffic type
                Numa node
Numa node            0
       0        34436.2

Measuring Loaded Latencies for the system
Using all the threads from each core if Hyper-threading is enabled
Using Read-only traffic type
Inject  Latency Bandwidth
Delay   (ns)    MB/sec
==========================
 00000  261.00    34039.8
 00002  247.58    34737.3
 00008  242.66    35286.1
 00015  249.57    32481.7
 00050  171.17    32450.7
 00100  140.57    29431.4
 00200  121.62    19132.4
 00300  114.40    13857.9
 00400  112.77    10810.1
 00500  110.30     8899.2
 00700  106.80     6661.1
 01000  105.10     4726.4
 01300  102.67     3769.1
 01700  101.83     2994.4
 02500  100.50     2331.9
 03500  100.32     1852.8
 05000  100.91     1487.5
 09000  101.70     1099.6
 20000  100.74      845.2

**Σχόλια:** Χρησιμοποιώντας τα ποιό πάνω στοιχεία δημιούργησα 2 γραφικές παραστάσεις.Η μία είναι το Inject Delay με το Latency και η άλλη Inject Delay με το Bandwidth.
Παρατηρούμε ότι όσο αυξάνεται το Inject Delay το Bandwidth μειώνεται.Όταν εισάγετε delay στο δίκτυο, το bandwidth-delay product αυξάνεται, οδηγώντας σε μείωση του effective bandwidth του δικτύου.
Επίσης από την άλλη γραφική παρατηρούμε ότι και το Latency μειώνεται καθώς αυξάνεται το Inject Delay αλλά από ένα σημείο και μετά σταθεροποιείται κοντά στα 100 ns.


Χρησιμοποιώντας την εντολή cat /proc/meminfo  έχουμε το εξής αποτέλεσμα : MemTotal: 196701596 kB = 187.6 GB
 

==========================================================================================================================================

***2.Local Disk***

Χρησιμοποιώντας το ioping μπορούμε να μετρήσουμε το disk latency.
Με την εντολή $ ioping −c 20 /tmp/ βλέπουμε το latency για 20 αιτήματα.

--- /tmp/ (ext3 /dev/nvme0n1p1) ioping statistics ---
19 requests completed in 2.77 ms, 76 KiB read, 6.85 k iops, 26.8 MiB/s
generated 20 requests in 19.0 s, 80 KiB, 1 iops, 4.21 KiB/s
min/avg/max/mdev = 87.6 us / 146.0 us / 162.2 us / 20.1 us

**Σχόλια:**Παρατηρούμε ότι το latency παραμένει σε όλα τα αιτήματα περίπου το ίδιο κοντά στα 150 us.Ακόμη και αν αυξήσουμε τα αιτήματα το μέσο latency παραμένει το ίδιο.

==========================================================================================================================================
Χρησιμοποιώντας το fio μπορούμε να μετρήσουμε το disk bandwidth.

fiotest: (g=0): rw=read, bs=(R) 4096B-4096B, (W) 4096B-4096B, (T) 4096B-4096B, ioengine=libaio, iodepth=64
fio-3.1
Starting 1 process
fiotest: Laying out IO file (1 file / 1024MiB)
fio: native_fallocate call failed: Operation not supported
Jobs: 1 (f=1)
fiotest: (groupid=0, jobs=1): err= 0: pid=10524: Thu Feb  8 15:25:30 2024
   read: IOPS=201k, BW=786MiB/s (824MB/s)(1024MiB/1303msec)
   bw (  KiB/s): min=676160, max=834336, per=93.85%, avg=755248.00, stdev=111847.32, samples=2
   iops        : min=169040, max=208584, avg=188812.00, stdev=27961.83, samples=2
  cpu          : usr=30.03%, sys=69.97%, ctx=5, majf=0, minf=76
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=0.1%, 16=0.1%, 32=0.1%, >=64=100.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.1%, >=64=0.0%
     issued rwt: total=262144,0,0, short=0,0,0, dropped=0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=64

Run status group 0 (all jobs):
   READ: bw=786MiB/s (824MB/s), 786MiB/s-786MiB/s (824MB/s-824MB/s), io=1024MiB (1074MB), run=1303-1303msec

Disk stats (read/write):
  nvme0n1: ios=215769/0, merge=0/0, ticks=2572/0, in_queue=1452, util=86.28%

  **Σχόλια:**Εδώ βλέπουμε ότι το bandwidth είναι 824 MB/s.

==========================================================================================================================================
  Χρησιμοποιώντας το hdparm μπορούμε να μετρήσουμε το disk capacity.

Χρησιμοποίησα την εντολή sudo fdisk -l και είχα τα εξής αποτελέσματα :

Disk /dev/sda: 1.1 TiB, 1200243695616 bytes, 2344225968 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x90909090

**Σχόλια:**Βλέπουμε ότι το μέγεθος του δίσκου είναι 1.1 TiB.

==========================================================================================================================================

***3. Remote memory and disk***

Χρησιμοποιώντας το ping μπορούμε να βρούμε το network latency.

StylAnt@node0:~$ ping node1
PING node1-link-1 (10.10.1.2) 56(84) bytes of data.
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=1 ttl=64 time=0.237 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=2 ttl=64 time=0.140 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=3 ttl=64 time=0.169 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=4 ttl=64 time=0.174 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=5 ttl=64 time=0.169 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=6 ttl=64 time=0.133 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=7 ttl=64 time=0.170 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=8 ttl=64 time=0.168 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=9 ttl=64 time=0.169 ms
64 bytes from node1-link-1 (10.10.1.2): icmp_seq=10 ttl=64 time=0.158 ms
^C
--- node1-link-1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9220ms
rtt min/avg/max/mdev = 0.133/0.168/0.237/0.030 ms

**Σχόλια:**Εδώ βλέπουμε ότι το latency κυμαίνεται απο 0.133 ms μέχρι 0.237 ms και με μέσο όρο 0.168 ms.

==========================================================================================================================================
Χρησιμοποιώντας το iperf μπορούμε να βρούμε το network bandwidth.

StylAnt@node1:~$ iperf -c node0 -e -i 1 -u -b 4.5g
------------------------------------------------------------
Client connecting to node0, UDP port 5001 with pid 6662
Sending 1470 byte datagrams, IPG target: 2.61 us (kalman adjust)
UDP buffer size:  208 KByte (default)
------------------------------------------------------------
[  3] local 10.10.1.2 port 46998 connected with 10.10.1.1 port 5001
[ ID] Interval            Transfer     Bandwidth      Write/Err  PPS
[  3] 0.0000-1.0000 sec   498 MBytes  4.18 Gbits/sec  355387/0   355386 pps
[  3] 1.0000-2.0000 sec   521 MBytes  4.37 Gbits/sec  371616/0   371616 pps
[  3] 2.0000-3.0000 sec   521 MBytes  4.37 Gbits/sec  371843/0   371843 pps
[  3] 3.0000-4.0000 sec   522 MBytes  4.38 Gbits/sec  372076/0   372076 pps
[  3] 4.0000-5.0000 sec   522 MBytes  4.38 Gbits/sec  372150/0   372150 pps
[  3] 5.0000-6.0000 sec   521 MBytes  4.37 Gbits/sec  371747/0   371747 pps
[  3] 6.0000-7.0000 sec   521 MBytes  4.37 Gbits/sec  371600/0   371600 pps
[  3] 7.0000-8.0000 sec   521 MBytes  4.37 Gbits/sec  371579/0   371579 pps
[  3] 8.0000-9.0000 sec   521 MBytes  4.37 Gbits/sec  371555/0   371555 pps
[  3] 9.0000-10.0000 sec   521 MBytes  4.37 Gbits/sec  371545/0   371545 pps
[  3] 0.0000-10.0000 sec  5.07 GBytes  4.35 Gbits/sec  3701101/0   370109 pps
[  3] Sent 3701101 datagrams
[  3] Server Report:
[  3]  0.0-10.0 sec  5.06 GBytes  4.35 Gbits/sec   0.003 ms 5373/3701101 (0.15%)

**Σχόλια:**Εδώ βλέπουμε το network bandwidth να είναι περίπου 4.35Gbits/sec.
==========================================================================================================================================

***Γενικά σχόλια:***
Bandwidth(MB/sec)	Latency(ns)	Capacity(GB)
      14571	        141.1	      187.6      1.Local Memory
      824	        150000	      1209.5     2.Local Disk
      556.8	        168000	        -        3.Remote memory and disk

      Capacity:Τιμές για capacity έχουμε μόνο για το Local Memory και το Local Disk όπου βλέπουμε ότι το μέγεθος του δίσκου να είναι μεγαλύτερος πράγμα που περιμέναμε .

      Latency:Το latency επίσης όπως περιμέναμε είναι πολύ πιο μικρό στο Local Memory αφού έχει πρόσβαση πιο γρήγορα από τα άλλα δύο.

      Bandwidth:Το bandwidth επίσης όπως περιμέναμε είναι πολύ γρήγορο στο Local Memory αφού είναι απευθείας ενωμένο με το CPU.






