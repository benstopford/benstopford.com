---
title: "Looking at Intel Xeon Phi (Kinghts Corner)"
date: 2012-08-09
categories: 
  - "blogroll"
  - "products"
slug: "looking-at-intel-xeon-phi-kinghts-corner"
---

**Characteristics:**

- Intel's new MIC 'Knights Corner' coprocessor (in the Intel Xeon Phi line) is targeted at the high concurrency market, previously dominated by GPGPUs, but without the need for code to be rewritten into Cuda etc (note Knights Ferry is the older prototype version).
- The chip has 64 cores and 8GBs of RAM with a 512b vector engine. Clock speed is ~ 1.1Ghz and have a 512k L1 cache. The linux kernel runs on two 2.2GHZ processors.
- It comes on a card that drops into a PCI slot so machines can install multiple units.
- It uses a [MESI protocol](http://en.wikipedia.org/wiki/MESI_protocol) for cache coherence.
- There is a slimmed down linux OS that can run on the processor.
- Code must be compiled to two binaries, one for the main processor and one for Knights Corner.
- Compilers are currently available only for C+ and Fortran. Only Intel compilers at present.
- It's on the cusp of being released (Q4 this year) for NDA partners (though we - GBM - have access to one off-site at Maidenhead). Due to be announced at the Supercomputing conference in November(?).
- KC is 4-6 GFLOPS/W - which works out at 0.85-1.8 TFLOPS for double precision.
- It is expected to be GA Q1 '13.
- It's a large 'device' the wafer is a 70mm square form-factor!
- Access to a separate board over PCI is a temporary step. Expected that future versions will be a tightly-coupled co-processor. This will also be on the back of the move to the 14nm process.
- A single host can (depending on OEM design) support several PCI cards.
- Similarly power-draw and heat-dispersal an OEM decision.
- Reduced instruction set e.g. no VM support instructions or context-switch optimisations.
- Performance now being expressed as GFlops per Watt. This is a result of US Government (efficiency) requirements.
- A single machine is can go faster than a room-filling supercomputer of '97 - ASIC\_Red!
- The main constraint to doing even more has been the limited volume production pipeline.
- Pricing not announced, but expected to be 'consistent with' GPGPUs.
- Key goal is to make programming it 'easy' or rather: a lot easier than the platform dedicated approaches or abstraction mechanisms such as OpenCL.
- Once booted (probably by a push of an OS image from the main host's store to the device) it can appear as a distinct host over the network.

**Commentary:**

- The key point is that Knights Corner provides most of the advantages of a GPGPU but without the painful and costly exercise of migrating software from one language to another (that is to say it is based on the familiar x86 programming model).
- Offloading work to the card is instructed through the offload pragma or offloading keywords via shared virtual memory.
- Computation occurs in a heterogeneous environment that spans both the main CPU and the MIC card which is how execution can be performed with minimal code changes.
- There is a reduced instruction set for Knights Corner but the majority of the x86 instructions are there.
- There is support for OpenCl although Intel are not recommending that route to customers due to performance constraints.
- Real world testing has shown a provisional 4x improviement in throughput using an early version of the card running some real programs. However results from a sample test shows perfect scaling.  Some restructuring of the code was necessary. Not huge but not insignificant.
- There is currently only C++ and Fortran interfaces (so not much use if you're running Java or C#)
- You need to remember that you are on PCI Express so you don't have the memory bandwidth you might want.

**References:**

[Good introduction to the history and development of Knights Corner](http://www.extremetech.com/extreme/133541-intels-64-core-champion-in-depth-on-xeon-phi)

[A second recent article on Knight Ferry/Corner](http://vr-zone.com/articles/intel-xeon-phi-b0-stepping--the-knight-in-shining-armor-/16871.html)

[Intel slides discussing KC and finishing wiht  a Black Scholes example](http://software.intel.com/sites/billboard/sites/default/files/BJ11_SFTS009_102_ENG_v02.pdf)

**Other things worth thinking about:**

[http://www.altera.com/](http://www.altera.com/)

Thanks to Mark Atwell  for his help with this post.
