---
title: "USEFUL, KEEP GOING: Measurements"
date: 2011-04-13
draft: true
slug: "id-700"
---

**File/Filer (netapp) measurements:**

10 1mb files took 80ms (local disk) 263ms (share) 100 1MB files took 485ms (local disk) 2.7s (share) 1000 1mb files took 3.740 (local disk) 25s (share)

10 10kb files took 10 ms (ld) 0.099 (share) 100 10kb files took 40ms (ld) 449ms (share) 1000 10kb files took 83ms (ls) 4.0s (share)

1000 1k files take 188ms (ld) 3s (share)

Filer:

\- Throughput=200Mb/s

\- Latency 3ms per per write (for all files smaller than 10KB).

(if you're interested local disk has a throughput of 100Mb/s and 6ms latency on our DL380)

Local Disk

2Gb/s 200us latency (this is interesting because it's ~ network (1Gb/s, 100us latency)

GC:

Program that adds byte arrays to a list, derefs then GCs.

Basically CMS is by far the way to go for large heaps:

1Gbheap; 500mb of objects 0.390: \[GC 262208K->282855K(1004928K), 0.2015710 secs\] 0.729: \[GC 545063K->569148K(1004928K), 0.2364600 secs\] 0.966: \[Full GC 569148K->548790K(1004928K), 0.3928250 secs\] Added 500 MB to the heap in 1242ms 1.367: \[GC 564416K->564598K(1004928K), 0.0138100 secs\] 1.381: \[Full GC 564598K->133K(1004928K), 0.0782680 secs\]

10Gb heap; 500mb of objects Added 1000 MB to the heap in 1050ms 1.205: \[GC 1103379K->192K(10048896K), 0.0016720 secs\] 1.206: \[Full GC 192K->133K(10048896K), 0.0166140 secs\] bash-3.00$ ./run 500 Added 500 MB to the heap in 510ms 0.664: \[GC 579068K->368K(10048896K), 0.0009780 secs\] 0.665: \[Full GC 368K->133K(10048896K), 0.0157250 secs\]

10Gb heap; 8Gb of objects

2.556: \[GC 2621504K->2821067K(10048896K), 1.9444930 secs\] 6.007: \[GC 5442571K->5718736K(10048896K), 2.3287900 secs\] 8.335: \[Full GC 5718736K->5512875K(10048896K), 4.3527380 secs\] 14.162: \[Full GC 8134379K->8094599K(10048896K), 6.2131420 secs\] Added 8000 MB to the heap in 20662ms 20.865: \[Full GC 8912959K->133K(10048896K), 1.2157430 secs\]

Using CMS: (10Gb heap; 8Gb of objects ...Many of 17.154: \[GC 8527410K->8527388K(10468736K), 0.1575430 secs\] Added 9500 MB to the heap in 20104ms 26.201: \[Full GC 10215003K->133K(10468736K), 1.3715960 secs\]
