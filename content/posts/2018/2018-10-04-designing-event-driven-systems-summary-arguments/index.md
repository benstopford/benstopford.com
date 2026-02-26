---
title: "Designing Event Driven Systems - Summary of Arguments"
date: 2018-10-04
categories: 
  - "blogroll"
slug: "designing-event-driven-systems-summary-arguments"
---

This post provides a terse summary of the high-level arguments addressed in [my book](https://www.confluent.io/designing-event-driven-systems).

### Why Change is Needed

_Technology has changed:_

- Partitioned/Replayable logs provide previously unattainable levels of throughput (up to Terabit/s), storage (up to PB) and high availability.
- Stateful Stream Processors include a rich suite of utilities for handling Streams, Tables, Joins, Buffering of late events (important in asynchronous communication), state management. These tools interface directly with business logic. Transactions tie streams and state together efficiently.
- Kafka Streams and KSQL are DSLs which can be run as standalone clusters, or embedded into applications and services directly. The latter approach makes streaming an API, interfacing inbound and outbound streams directly into your code.

_Businesses need asynchronicity:_

- Businesses are a collection of people, teams and departments performing a wide range of functions, backed by technology. Teams need to work asynchronously with respect to one another to be efficient.
- Many business processes are inherently asynchronous, for example shipping a parcel from a warehouse to a user’s door.
- A business may start as a website, where the front end makes synchronous calls to backend services, but as it grows the web of synchronous calls tightly couple services together at runtime. Event-based methods reverse this, decoupling systems in time and allowing them to evolve independently of one another.

_A message broker has notable benefits:_

- It flips control of routing, so a sender does not know who receives a message, and there may be many different receivers (pub/sub). This makes the system pluggable, as the producer is decoupled from the potentially many consumers.
- Load and scalability become a concern of the broker, not the source system.
- There is no requirement for backpressure. The receiver defines their own flow control.

Systems still require Request Response

- Whilst many systems are built entirely-event driven, request-response protocols remain the best choice for many use cases. The rule of thumb is: use request-response for intra-system communication particularly queries or lookups (customers, shopping carts, DNS), use events for state changes and inter-system communication (changes to business facts that are needed beyond the scope of the originating system).

_Data-on-the-outside is different:_

- In service-based ecosystems the data that services share is very different to the data they keep inside their service boundary. Outside data is harder to change, but it has more value in a holistic sense.
- The events services share form a journal, or ‘Shared Narrative’, describing exactly how your business evolved over time.

_Databases aren’t well shared:_

- Databases have rich interfaces that couple them tightly with the programs that use them. This makes them useful tools for data manipulation and storage, but poor tools for data integration.
- Shared databases form a bottleneck (performance, operability, storage etc.).

_Data Services are still “databases”:_

- A database wrapped in a service interface still suffers from many of the issues seen with shared databases (The Integration Database Antipattern). Either it provides all the functionality you need (becoming a homegrown database) or it provides a mechanism for extracting that data and moving it (becoming a homegrown replayable log).

_Data movement is inevitable as ecosystems grow._

- The core datasets of any large business end up being distributed to the majority of applications.  
- Messaging moves data from a tightly coupled place (the originating service) to a loosely coupled place (the service that is using the data). Because this gives teams more freedom (operationally, data enrichment, processing), it tends to be where they eventually end up.

### Why Event Streaming

_Events should be 1st Class Entities:_

- _Events are two things: (a) a notification and (b) a state transfer._ The former leads to stateless architectures, the latter to stateful architectures. Both are useful.
- _Events become a Shared Narrative describing the evolution of the business over time:_ When used with a replayable log, service interactions create a journal that describes everything a business does, one event at a time. This journal is useful for audit, replay (event sourcing) and debugging inter-service issues.
- _Event-Driven Architectures move data to wherever it is needed:_ Traditional services are about isolating functionality that can be called upon and reused. Event-Driven architectures are about moving data to code, be it a different process, geography, disconnected device etc. Companies need both. The larger and more complex a system gets, the more it needs to replicate state.

_Messaging is the most decoupled form of communication:_

- Coupling relates to a combination of (a) data, (b) function and (c) operability
- Businesses have core datasets: these provide a base level of unavoidable coupling.  
- Messaging moves this data from a highly coupled source to a loosely coupled destination which gives destination services control.

_A Replayable Log turns ‘Ephemeral Messaging’ into ‘Messaging that Remembers’:_

- Replayable logs can hold large, “Canonical” datasets where anyone can access them.
- You don’t ‘query’ a log in the traditional sense. You extract the data and create a view, in a cache or database of your own, or you process it in flight. The replayable log provides a central reference. This pattern gives each service the “slack” they need to iterate and change, as well as fitting the ‘derived view’ to the problem they need to solve.

_Replayable Logs work better at keeping datasets in sync across a company:_

- Data that is copied around a company can be hard to keep in sync. The different copies have a tendency to slowly diverge over time. Use of messaging in industry has highlighted this.
- If messaging ‘remembers’, it’s easier to stay in sync. The back-catalogue of data—the source of truth–is readily available.
- Streaming encourages derived views to be frequently re-derived. This keeps them close to the data in the log.

_Replayable logs lead to Polyglot Views:_

- There is no one-size-fits-all in data technology.
- Logs let you have many different data technologies, or data representations, sourced from the same place.

_In Event-Driven Systems the Data Layer isn't static_

- In traditional applications the data layer is a database that is queried. In event-driven systems the data layer is a stream processor that prepares and coalesces data into a single event stream for ingest by a service or function.
- KSQL can be used as a data preparation layer that sits apart from the business functionality. KStreams can be used to embed the same functionality into a service.
- The streaming approach removes shared state (for example a database shared by different processes) allowing systems to scale without contention.

_The ‘Database Inside Out’ analogy is useful when applied at cross-team or company scales:_

- A streaming system can be thought of as a database turned inside out. A commit log and a a set of materialized views, caches and indexes created in different datastores or in the streaming system itself. This leads to two benefits.
    - Data locality is used to increase performance: data is streamed to where it is needed, in a different application, a different geography, a different platform, etc.
    - Data locality is used to increase autonomy: Each view can be controlled independently of the central log.
- At company scales this pattern works well because it carefully balances the need to centralize data (to keep it accurate), with the need to decentralise data access (to keep the organisation moving).

_Streaming is a State of Mind:_

- Databases, Request-response protocols and imperative programming lead us to think in blocking calls and command and control structures. Thinking of a business solely in this way is flawed.
- The streaming mindset starts by asking “what happens in the real world?” and “how does the real world evolve in time?” The business process is then modelled as a set of continuously computing functions driven by these real-world events.
- Request-response is about displaying information to users. Batch processing is about offline reporting. Streaming is about everything that happens in between.

_The Streaming Way:_

- Broadcast events
- Cache shared datasets in the log and make them discoverable.
- Let users manipulate event streams directly (e.g., with a streaming engine like KSQL)
- Drive simple microservices or FaaS, or create use-case-specific views in a database of your choice

The various points above lead to a set of broader principles that summarise the properties we expect in this type of system:

### The WIRED Principles

**Windowed**: Reason accurately about an asynchronous world.

**Immutable**: Build on a replayable narrative of events.

**Reactive**: Be asynchronous, elastic & responsive.

**Evolutionary**: Decouple. Be pluggable. Use canonical event streams.

**Data-Enabled**: Move data to services and keep it in sync.
