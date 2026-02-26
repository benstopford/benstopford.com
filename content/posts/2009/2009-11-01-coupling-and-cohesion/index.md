---
title: "Coupling And Cohesion"
date: 2009-11-01
slug: "coupling-and-cohesion"
url: "/feature-articles/software-design/coupling-and-cohesion/"
---

**What do we mean by coupling between classes?**

Coupling is usually defined as the degree by which one class depends on others. This is usually measured through the number of references both in and out of different types (global data, protected variables etc).

**Why should we worry about coupling between classes?**

High coupling in a class implies that there is much dependence on other classes. This tends to imply that changing the class may force changes in the dependent classes. This is sometimes known as dependency rot.

Conversely Low coupling increases encapsulation, which makes classes easier to;

1. Understand and Change: Reducing dependency fan out from a class decreases the number of classes that must be considered to understand the function of the class.
2. Reuse: Low coupling increases encapsulation. This in turn makes a class easier to reuse as it has less associated ‘baggage’ to be considered.

Coupling is often considered in conjunction with Cohesion (see below) as design principals when reflecting on the responsibilities of a class. However there are no hard and fast rules. This is just a trade off to consider when making design decisions.

**How is coupling measured?**

In a metrics sense coupling is measured simply as the number of classes that are called or call a single class. The standard measure, originally proposed by Chidamber and Kemerer in the early 1990’s, takes this approach.

**So does the Type of coupling matter?**

Yes, very much so. The worst kind of coupling occurs when one class modifies the data in another class. Passing large (partially unused) data structures around is also found upon.

The types of coupling, in order of lowest to highest coupling, are as follows:

- Data coupling – Data coupling is when modules share data through, for example, parameters. Each datum is an elementary piece, and these are the only data which are shared (e.g. passing an integer to a function which computes a square root).
- Stamp coupling (Data-structured coupling) – Stamp coupling is when modules share a composite data structure, each module not knowing which part of the data structure will be used by the other (e.g. passing a student record to a function which calculates the student’s GPA).
- Control coupling – Control coupling is one module controlling the logic of another, by passing it information on what to do (e.g. passing a what-to-do flag).
- External coupling – External coupling occurs when two modules share an externally imposed data format, communication protocol, or device interface.
- Common coupling – Common coupling is when two modules share the same global data (e.g. a global variable).
- Content coupling – Content coupling is when one module modifies or relies on the internal workings of another module (e.g. accessing local data of another module). (ref)

So far we have been considering static coupling. What about dynamic coupling. Why might this add complexity to the issue?

By considering Dynamic coupling we are considering temporal dependencies (i.e. dependencies in execution order) between classes. Problems can occur in highly coupled classes when they expose observable state as the order (or even worse the timing) in which individual methods on a class are called must be maintained.

**So what about cohesion?**

Cohesion is a measure of how strongly related and focused the responsibilities of a class are.

The general design principal is to promote Low Coupling and High Cohesion.

Cohesion from a metric point of view is measured by comparing the use of variables within a class.

- If two methods use the same variable this will act to decrement the cohesion rating.
- If one method uses a variable that the other does not this increases the cohesion measure.

Cohesion measures of 0ne are ideal. However it should be noted that this is by no means an ideal metric and its measurement can be misleading in certain circumstances.

[See Wikipedia Definition](http://en.wikipedia.org/wiki/Cohesion)
