---
title: "SSD performance 2015"
date: 2015-05-01
slug: "ssd-performance-2015"
---

There is significant benefit to be found using SSD's these days. The benefits come largely from concurrency, as well as faster PCIe access. There are some numbers below that show the difference in read and write performance over sequential and random workloads.

SATA is limited to 750MB/s. PCIE goes up to 40GB/s which is pretty much the bandwidth of the memory bus. SSD's typically demonstrate latencies around 60 times faster than spinning drives.

Below are a three typical commodity drives as of April 2015. The SSD quoted uses a PCIe mount which makes the comparison pretty unfair, so just take that into account. Like all media they still operate better for sequential workloads where prefetching aids throughput. So whilst SSD's undoubtably change these problems note the price performance. The Seagate HDD holds 8TBs. The SSD only 0.5TB.

**Samsung XP941 SSD** (£300 0.5TB) - A fast PCIe mounted SSD drive

**Seagate Archive 8TB HDD** (£242) - A high storage mechanical drive

**Western Digital Raptor 1TB HDD** (£178) - a low storage, fast mechanical drive

**Sequential Read**

- Sam HDD ~ 190MB/s
- Rap HDD ~ 203MB/s
- XP941 SSD ~ 1,485 MB/s

**Sequential Write**

- Sam HDD ~ 190MB/s
- Rap HDD ~ 203MB/s
- XP941 SSD ~ 710 MB/s

**Random Read**

- Sam HDD ~ 300 reads/sec
- Rap HDD – 270 reads/sec
- XP941 SSD ~ 80,000 reads/sec

**Random write**

- Sam HDD ~ 402 writes/sec
- Rap HDD ~ 366 writes/sec
- XP941 SSD ~ 50,000 writes/sec

**Some more general figures taken specifically for 4k pages**

SSD random write ~ 25k 4k w/s => 100MB/s SSD random read ~ 80k 4k ws => 300MB/s

SSD seq write 450 MB/s SSD seq read ~ 500 MB/s

HDD random write 300 I0/s => 1.2Mb/s @4k page HDD random read 300 I0/s => 1.2Mb/s @4k page

HDD seq write 230MB/s HDD seq read 230MB/s

Other useful resources:

1. [SSD sequential vs random](http://codecapsule.com/2014/02/12/coding-for-ssds-part-5-access-patterns-and-system-optimizations/)
2. [Views from Acunu (pre acquisition)](https://web.archive.org/web/20120729224435/http://www.acunu.com/2/post/2011/08/why-theory-fails-for-ssds.html)
