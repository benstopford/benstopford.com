---
title: "TODO: Thoughts on SSDs etc"
date: 2014-04-13
draft: true
slug: "id-2029"
---

Graphic here: [/uploads/PastedGraphic-2.pdf](/uploads/PastedGraphic-2.pdf)

Throughput: Decent disk (RAID/FibreChannel) stream sequential data at a maximum of 0.5GB/s\*. Fast ram is will produce 8GB/s. This assumes sequential access and this distinction is important, so with sequential access there ins't much in it, we can saturate our CPU's thirst for data. The problem is that sequential access is unrealistic. We rarely

Latency: Latency is very different. RAM addressing is around 80ns (although caches often make this look smaller), SSD is 30us, Disk seek time (if you can't avoid it) is around 800us.

SSD will do about 35k 4k reads per second in a random scatter. That's 140MB/s

So what about random access in bulk. RAM really doesn't care. There is a small affect from prefetching but it's less than 10% typically. SSD card will do 120k IOPS on a 4k page. Assuming the message is 4K that gives us 120k trades per second x Number of disks (20). So we should be able to extact about 2 million small xml files per second.

Now a query in ODC for teh full dataset is a few mins anyway.wo

The problem is that you can't always have sequential access. If the information you are trying to get as is dotted over a large number of pages they drives will have to seek frequently you will bring back a large amounts of data (in the pages) that is probably not relevant.

SSD's address the random access problem somewhat. In our case we should be able to put an XML document sequentially on flash and get at it in around 50us in a consistent manner because this is a simple use case.

Not sure if theat helps but

\* based on measurements taken through an oracle server ([here](http://kevinclosson.wordpress.com/2012/02/27/modern-servers-are-better-than-you-think-for-oracle-database-part-i-what-problems-actually-need-fixed/))

Slightly old but thorough comparision of drive performance: [http://www.pdl.cmu.edu/PDL-FTP/PDSI/simsa-pdsw08.pdf](http://www.pdl.cmu.edu/PDL-FTP/PDSI/simsa-pdsw08.pdf)

~100 times faster for random reads of 4k-64k dropping with disk staying approx constant over range and FIO dropping by a factor fo 4 between 4 & 64KB

\=> good summary is taht they are 100 times faster in 2008 for 4k reads (21,000 vs 291 IOPS)

Example of why sequential kicks ass

[http://queue.acm.org/detail.cfm?id=1563874](http://queue.acm.org/detail.cfm?id=1563874)

(these are old ssd drives and are connected over sata. the hdd are connected over sas which is double speed (6gb/s vs 3gb/s)

Also the random reads are taking  4 byte ints at random from teh drive. This is an unrealistically small payload, suitable if you can comparing to RAM but more interesting woudl be random access tests for larger blocks - maybe 1-4k

Example of DB's not working well with SSD

THC-H (100GB)

HP Report [http://h20195.www2.hp.com/v2/GetPDF.aspx/4AA0-0248ENW.pdf](http://h20195.www2.hp.com/v2/GetPDF.aspx/4AA0-0248ENW.pdf)

states taht HDD Random IO is 340io/s vs 120k for fusion io => 3000 times faster.

Servers

Fusion-io ioDrive result with the ProLiant DL380 G6: [http://www.tpc.org/tpch/results/tpch\_result\_detail.asp?id=109090801](http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109090801 "http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109090801") Solid state drive result with the ProLiant DL380 G6: [http://www.tpc.org/tpch/results/tpch\_result\_detail.asp?id=109082801](http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109082801 "http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109082801") SAS rotational drive result with the ProLiant DL380 G6: [http://www.tpc.org/tpch/results/tpch\_result\_detail.asp?id=109100801](http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109100801 "http://www.tpc.org/tpch/results/tpch_result_detail.asp?id=109100801")

[http://www.tpc.org/tpch/results/tpch\_price\_perf\_results.asp?print=true&resulttype=NONCLUSTER&version=2%&currencyID=1](http://www.tpc.org/tpch/results/tpch_price_perf_results.asp?print=true&resulttype=NONCLUSTER&version=2%&currencyID=1)

6, 7, 10 respectively on report here  [http://www.tpc.org/tpch/results/tpch\_price\_perf\_results.asp?print=true&resulttype=NONCLUSTER&version=2%&currencyID=1](http://www.tpc.org/tpch/results/tpch_price_perf_results.asp?print=true&resulttype=NONCLUSTER&version=2%&currencyID=1)

(see pic at bottom)

But finally the point is that you have a combination of the two.... so if you can leveage teh 3000 times faster access times of SSD
