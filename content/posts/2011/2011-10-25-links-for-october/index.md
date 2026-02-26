---
title: "Interesting Links Oct 2011"
date: 2011-10-25
categories: 
  - "blogroll"
  - "links"
slug: "links-for-october"
---

**High Performance Java**

- Not exactly lightweight reading but one of the most detailed and influential papers on tuning your software for processing efficiency: [What Developers Should Understand About Memory](http://www.akkadia.org/drepper/cpumemory.pdf)
- If you read the above and want to put some of it into action then VTune should be your next port of call. Diagnostic software for CPU cache hits etc: [VTune™ Amplifier XE 2011 from Intel - Intel® Software Network](http://www.google.com/url?q=http%3A%2F%2Fsoftware.intel.com%2Fen-us%2Farticles%2Fintel-vtune-amplifier-xe%2F&sa=D&usg=AFQjCNE76LmyWEYfMg-e4OOogSDPaRpbhg)
- When it really won’t go any faster, look at the Assembler: [Deep dive into assembly code from Java | Java.net](http://www.google.com/url?q=http%3A%2F%2Fweblogs.java.net%2Fblog%2Fkohsuke%2Farchive%2F2008%2F03%2Fdeep_dive_into.html&sa=D&usg=AFQjCNF9j3TVTfxhXRTO7rBDsixnDOSp2g)
- In anticipation of G1 (in case they ever get it finished) here’s the original paper with anticipated performance figures: [G1 paper with figures](http://www.google.com/url?q=http%3A%2F%2Fciteseerx.ist.psu.edu%2Fviewdoc%2Fdownload%3Fdoi%3D10.1.1.63.6386%26rep%3Drep1%26type%3Dpdf&sa=D&usg=AFQjCNEidAH-zqps3zmyz8k-UKn_2KRa6Q)
- A different approach to GC using processor specific minor collections (in Haskell): [Multicore Garbage Collection with Local Heaps](http://www.google.com/url?q=http%3A%2F%2Fcommunity.haskell.org%2F~simonmar%2Fpapers%2Flocal-gc.pdf&sa=D&usg=AFQjCNF1hXVUo8GzoztALbEtuLezvohzqA)

**Distributed Data Storage:**

- The new Oracle NoSQL database – this is the best article I’ve read summarising it’s position in the market: [DBMS Musings: Overview of the Oracle NoSQL Database](http://www.google.com/url?q=http%3A%2F%2Fdbmsmusings.blogspot.com%2F2011%2F10%2Foverview-of-oracle-nosql-database.html&sa=D&usg=AFQjCNFcoAUZh-ZS7foxTaKtfnKaAZhM_g)
- The official Oracle NoSQL Whitepaper: [Oracle NoSQL Database White Paper](http://www.oracle.com/technetwork/database/nosqldb/learnmore/nosql-database-498041.pdf)
- An interesting approach to data storage: an FPGA based data warehouse: [FPGA Data Warehouse](http://www.google.com/url?q=http%3A%2F%2Fwww.cse.buffalo.edu%2F~vipin%2Fpapers%2F2010%2Ftodd1.pdf&sa=D&usg=AFQjCNGDdiZ0xR89Uzsi8S3e_Y2dV_Lmhg)
- Google’s interesting SQL wrapped MapReduce framework: [Tenzing A SQL Implementation On The MapReduce Framework](http://www.google.com/url?q=http%3A%2F%2Fresearch.google.com%2Fpubs%2Fpub37200.html&sa=D&usg=AFQjCNHQz67AXSon61tegck3Y5tG2Hqkkg)

**Distributed Computing:**

- The Actors Model - just in case you’re not familiar with it: [Actors model for distribution](http://www.google.com/url?q=http%3A%2F%2Fdspace.mit.edu%2Fbitstream%2Fhandle%2F1721.1%2F6952%2FAITR-844.pdf%3Fsequence%3D2&sa=D&usg=AFQjCNFu9U89c4XHaN_HwNuiaX_QxjOA4Q)
- Gluster – an open source distributed file system: [Gluster](http://www.google.com/url?q=http%3A%2F%2Fwww.gluster.org%2Fabout%2F&sa=D&usg=AFQjCNHqZ1-JuPfVLZPHXoCtdyYeXyHjFQ)
- Running Cuda natively on x86 processors: [Running CUDA Code Natively on x86 Processors | Dr Dobb's Journal](http://www.google.com/url?q=http%3A%2F%2Fdrdobbs.com%2Fhigh-performance-computing%2F231500166&sa=D&usg=AFQjCNF4faA5OmAD4M1mOwk8W7ohlxhFxw)

**Coherence related:**

- Thinking about using 64bit JVMs with compressed pointers : [32-bit or 64-bit JVM? How about a Hybrid?](http://www.google.com/url?q=http%3A%2F%2Fblog.juma.me.uk%2F2008%2F10%2F14%2F32-bit-or-64-bit-jvm-how-about-a-hybrid%2F&sa=D&usg=AFQjCNFX2AQwBuyYd9vCqtnUR8pJ5-hm3Q)
- Using different caches for read and write. A sensible pattern for Cohernece implementation:  [Alexey Ragozin’s Blog](http://aragozin.wordpress.com/2009/04/22/cache-for-read-and-cache-for-write/)
- OCZ Z-Drive – an interesting and competitively priced alternative to FusionIO:
    - [OCZ Z-Drive R4 C PCI Express 1.6TB SSD Review](http://www.google.com/url?q=http%3A%2F%2Fthessdreview.com%2Four-reviews%2Focz-z-drive-r4-pci-express-1-6tb-ssd-review%2F3%2F&sa=D&usg=AFQjCNEZpF7aZEO7a9s_vF8iJjFSdG_dsA)
    - [OCZ Z-Drive R4 review roundup](http://www.google.com/url?q=http%3A%2F%2Fm.engadget.com%2Fdefault%2Farticle.do%3FartUrl%3Dhttp%3A%2F%2Fwww.engadget.com%2F2011%2F09%2F28%2Focz-z-drive-r4-review-roundup-this-is-what-2-800mb-s-looks-like%2F%26category%3Dclassic%26postPage%3D4%26icid%3Deng_latest_art&sa=D&usg=AFQjCNFewaoDd8wE8B87N7Z_em5e9aJxaA)

**Just Interesting:**

- The architecture of the transputer. An interesting reflection on a couple of Bristol’s finest exports (other than Portishead): the  Transputer and the Occum programming language. [David May, parallel processing pioneer • reghardware](http://www.google.com/url?q=http%3A%2F%2Fwww.reghardware.com%2F2011%2F08%2F18%2Fheroes_of_tech_david_may%2F&sa=D&usg=AFQjCNHFH9xmF7J7Dxo1gTdH1XWrIJWBOA)
- Is your brain like an Iphone? [Is Your Brain Like an iPhone? Which App is Running Now? - Novato, CA Patch](http://www.google.com/url?q=http%3A%2F%2Fnovato.patch.com%2Fblog_posts%2Fis-your-brain-like-an-iphone-which-app-is-running-now&sa=D&usg=AFQjCNF-HxIaTUCTWEnlc8r9LuDpmOVDpQ)
- Just be still for once: [No Shame in Stillness « Under the Apricot Tree](http://www.google.com/url?q=http%3A%2F%2Fundertheapricottree.wordpress.com%2F2011%2F10%2F18%2Fno-shame-in-stillness%2F&sa=D&usg=AFQjCNEfBD0QXrGGXLZuxGNlgx5pga1XbQ)
- Of the huge amount of writing about Steve Jobs I thought the Economist’s coverage was the best: [Steve Jobs: The magician | The Economist](http://www.google.com/url?q=http%3A%2F%2Fwww.economist.com%2Fnode%2F21531529&sa=D&usg=AFQjCNGk2MSjCdwsBiuOM-DUypZPHKMDWg)
- Scott Marcar’s thought prevoking dialog on technology through a financial crisis:  [The Long Haul: Scott Marcar Leads RBS' Tech Team Through the Financial Crisis- WatersTechnology.com](http://www.google.com/url?q=http%3A%2F%2Fwww.waterstechnology.com%2Fwaters%2Ffeature%2F2107476%2Fhaul-scott-marcar-leads-rbs-tech-team-financial-crisis&sa=D&usg=AFQjCNHFpohlurAdX45LdNxLCGmVk2e68Q)
- Short but thought provoking article on company culture: [Why You Should Question Your Culture - Ron Ashkenas - Harvard Business Review](http://www.google.com/url?q=http%3A%2F%2Fblogs.hbr.org%2Fashkenas%2F2011%2F10%2Fwhy-you-should-question-your-c.html%3Futm_source%3Dfeedburner%26utm_medium%3Dfeed%26utm_campaign%3DFeed%3A%2Bharvardbusiness%2B\(HBR.org\)&sa=D&usg=AFQjCNEA05R3NBBLum_p-G0nx5ZfXvyzkA)
