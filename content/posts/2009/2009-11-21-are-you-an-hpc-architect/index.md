---
title: "Four HPC Architecture Questions - With Answers"
date: 2009-11-21
categories: 
  - "team-development"
tags: 
  - "coherence"
  - "datasynapse"
  - "high-performance-computing"
  - "hpc"
  - "interviewing"
  - "questions"
slug: "are-you-an-hpc-architect"
---

These were originally given as part of the RBS Enterprise Engineering Program with teams attaching each one and presenting back to the group. They could make good basis for a longer worked question in interview ... or maybe you just fancy testing your brain??<!--more-->

These scenarios are open ended and can be answered to different levels in different ways. The key point is to have a think about fundamental issues that affect performance and scalability in each scenario. Then try and fit the technologies to them.

### **Scenario 1 :**

System A calculates market risk real time on trades and market data that are currently stored in a Coherence cache. Currently the client application requests the trade and market data from the cache and computes the risk locally on the trader workstation. They are eight-core machines so this is practical but the intention is to scale this out. The market data cache contains 1000 x 3k objects. The trade cache contains 5,000,000 x 50k objects. Trades and market data change intraday.

HPC have been asked to consider this problem with the view of minimising the latency incurred pricing trades. They are considering solutions that use the compute grid + data grid and solutions that use the data grid on its own. Sketch out a compute grid + data grid solution and a data grid only solution. Reflect on the pros and cons of each. Which would you go for?

### **Scenario 2:**

System B is a web application. Its homepage returns a set of trades from the database based on the users’ profile. The application then keeps this up to date as trades change in the system. The home page takes up to 60s to load. There are 10 users and 50,000 x 3k trades. Users are very unhappy with how long the home page takes to load. The architect on the team is keen to bolt a caching layer in front of the database so you’ve been called in to advise.

Paying note to the current performance of the system and its architecture, what would you suggest that the team do?

### **Scenario 3:**

System C is a web based retail banking system such as the sort you might use to manage your finances. It is currently a simple three tiered application with load balanced application servers in the middle tier and 5 machine Oracle RAC cluster at the back end. Another bank has just been acquired and the business wants to roll their users in, doubling the load on the system. Regularly accessed user data is currently in the 100GB range. Suggest a solution that might accommodate these changes. In your answer consider the merits of using the following:

- Scaling out Oracle RAC to 10 servers.
- Using a replicated or partitioned cache to hold state.
- Using the Compute Grid to scale out the application tier.
- Using a messaging system.

### **Scenario** **4:**

System D is a trading application that requires users to be able to conduct “what if” modelling by perturbing either market data and/or trade parameters. This is used in two ways, to determine fair pricing prior to trade execution and to examine the overall risks associated with the trader’s individual position. Currently the trading desk has ten users and is located in a single location. Response time is a critical factor as is the ability to handle instruments of widely differing complexity and duration. Detail the factors that should be considered so as to ensure that the solution meets performance requirements. On the basis of these propose a design for the system ensuring that it can scale with increased number of traders, locations, counterparties and instrument types.

Some sample answers can be found [here](/sample-answers-are-you-an-hpc-architect/).
