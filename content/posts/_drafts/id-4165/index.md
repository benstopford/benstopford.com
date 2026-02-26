---
title: "When Consistency Matters, and More Importantly, When it Does Not"
draft: true
---

Whether you _need_ a consistent view of your data is something worth very careful consideration. Intuitively one feels natural and homely. We have grown up surrounded with databases that provide this convenient feature. As a general rule this is because databases provide a wealth of features for authoring and collaborating on state. The 'transferring money from one account to another' example is typical of this. But there are an increasing number of scenarios that don't author data in this way. They simply collect it. Process it. Produce a result.

Such scenarios generally don't require the complexity of a single consistent view. Also, from a design perspective, it's often better to collaborate by passing messages rather than using shared mutable state.

A well touted and reasonable argument for consistency is that we may as well have it because it solves probelms for us. The problem is that consistency is the relm of the database. ???

This becomes more apparent when we consider the practicalities of specific use cases. Lets take the classic example of you banking web client. You move money from one account to another. If the transaction is not atomic, consistent and isolated you might be able to transfer the same money into two separate accounts at the same time doubling your holdings. Tactics like [this](http://www.infoq.com/news/2014/04/bitcoin-banking-mongodb) have been used with some success against a number of Bitcoin repositories.  The problem occurs when a single action contains multiple, discrete side effects. It is the reason we have transactions.

One potential solution which doesn't require transactions is to use versions to determine whether a subsequent change has affected the data since we read it. This is known as multi version concurrency control (MVCC). Each entry has a version. Writes throw an error in the version has changed since it was originally read.

This would solve this problem because the second 'decrement' operation would fail and the action would fail as a whole.

However what if the situation was flipped and collating money into one account from two separate ones. We'd decrement the two originating accounts. We'd increment the destination account. The second operation would fail due to a version inconsistency. At this point we could either fail completely, which would involve rolling back the original change as a database transaction would, or we could retry, which in this case would work fine. The increment would reapply to the later item.

The problem is that retries work in simple cases, but can fail horribly in more complex ones. More specifically, this works only when actions are commutative. I won't further this example but consider what would happen if instead of transferring funds (a commutative action) we were applying a percentage discount (a non-commutative action). Suffice to say we'd get different results depending on the order of execution. You can imagine how this problem worsens as more values are involved.

So one problem comes from the order of execution where actions operate on more than one value in the database.

You can work around these problems with careful planning, but for everyday systems that author data they can brittle and generally make people's heads hurt. This is why databases have transactions.

But a vast amount of data processing does not follow this pattern at all.

The point however is that many applications are not banking systems. They can manage with simpler data models that don't necessitate these kinds of issues. Many systems can manage with single entity transactions. Many more with grouped commutative actions. If they have non-commutative actions they can often be restricted only to a single entity so that simple MVCC will suffice.

If this is the case, we can open up a lot of different nice options.

If we can guarantee ordering of complete actions we get the same result. This is termed serialisability.

So there is opportunity to replace a global consistent view with the less arduous requirement of ordered changes. Think about what a traditional database does. It allows different processes to change data. It will ensure exclusion between these changes, so that the results are deterministic. Read, modify, write etc. It will do this across different entities in the database. This is a holistic, general and really quite beautiful solution. Alas, as with many general solutions, they can become hard to implement at scale. In fact true serialisability is rarely applied in any database for performance reasons.
