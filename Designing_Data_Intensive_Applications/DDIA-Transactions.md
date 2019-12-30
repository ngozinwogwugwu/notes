# Transactions
Transactions are a way of making our databases more fault-tolerant. It's important to handle some failures at a database level, so that an application can model database read/writes simply. However, it's important to recognize that transactions are expensive, so they're not always the best option.
- **Transactions**: ways to group multiple reads & writes into one logical unit
- **Commits**: how we make everything official once a transaction succeeds
- **to abort/roll back**: if a transaction fails, don't do anything

Chapter sections:
- [Overview of Transactions](#overview-of-transactions)
- [Weak Isolation Levels](#weak-isolation-levels)
- [Serializability](#serializability)

## Overview of Transactions
transactions have been present in relational databases since 1975. NoSQL databases have weaker transactions, but it makes them more scalable

### ACID
***ATOMICITY, CONSISTENCY, ISOLATION, DURABILITY***

This is a way to describe fault-tolerant databases, but it's not unambiguous. Different databases are "ACID compliant" in different ways. One alternative to ACID is **BASE**, (**B**asically **A**vailable, **S**oft State, **E**ventually Consistent), which is even more vague a name than ACID.
- **Atomicity**: grouping writes together in an _atomic transaction_
- **Consistency**: A system is consistent if certain "invariants" are satisfied at the beginning and end of each transaction. Examples of an invariant include unique IDs or balances across an accounting system balancing out.
- **Isolation**: a guarantee that if you have two processes read/writing at the same time, they won't interfere with each other
  - **Serializability**: A more formal version of isolation. It means that processes running in parallel can be modeled as running in series
- **Durability**: A guarantee that once a transaction has been committed, the data you wrote is safe

### Single & Multi-Object Operations
**Multi-Objects Operation**: ACID guarantees for when you're making updates to multiple objects

Relational databases determine what operations are part of the same transaction based on a client's TCP connection. NoSQL databases, by contrast, don't have a great way of grouping operations into a transaction


## Weak Isolation Levels



## Serializability