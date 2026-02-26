---
title: "Intuitions for Scaling"
draft: true
---

Reference to this ACM paper (which is excellent by the way) comes up a fair bit on the internet as it's a great example of the slightly counterintuitive fact that disk can be faster than RAM.

To get a feeling for this it's easy to illustrate in a simple program:

...

With sequential access being 200x faster it's worth bearing in mind. Iterating over a hash map for example... does that work like sequential access??

SSD's are up to 10-200x faster than magnetic drives for random reads (depending on whether you go for commodity or FusionIO). Having said that even fusion io is 1000x slower than ram for random access.

http://www.pdsi-scidac.org/events/PDSW08/resources/papers/simsa\_PDSW.pdf

 

\---

False sharing is beautifully covered here. http://daniel.mitterdorfer.name/articles/2014/false-sharing/

\=> 3x speedup in java using @Contended annotation
