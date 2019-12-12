# Partitioning
partitioning is good for _scalability_, and usually different partitions go on different nodes
- **shared-nothing cluster**: a setup where a single record goes on only one partition
- when a database is partitioned, datasets and query loads get distributed
- it's normal to combine partitioning with replication. It means that you can have a partition replicated on multiple nodes

## Partitioning Key-value Data
If you can spread data across _n_ nodes, then a query (in theory) should take _1/n_ times the time
- **skewed**: a situation where data is not spread evenly
- **hot spot**: a partition with a disproportionately high load
We want to avoid skewed partitions because they kind of defeat the purpose of partitions. A brute-force way to avoid skews is to assign records to partitions at random, but then you wouldn't know which partition to query, which would also defeat the purpose of a partitioned database. Although you can't completely prevent skewed workloads, solutions include partitioning by key range and key hash:

### Partitioning by Key Range
When you partition your database by key range, you assign a key range to each partition - this is analagous to a paper encylopedia.
- **partition boundaries** determine which partition contains which key. This can be chosen automatically or by an administrator
- advantave of partitioning by key range: you can keep keys within a partition in sorted order. This makes range scans fast.
- disadvantage: Sorting records by keys can lead to hotspots in many situations 

### Partitioning by Key Hash
If you hash your keys before saving them, you prevent hotspots. The downside is that you loose the ability to make fast range queries.
- you don't need a strong hashing funciton. You don't even need something that's completely unpredictable
- **compound primary key**: when only part of your key gets hashed

## Secondary Indexes
You use secondary indexes to search by value. You need to consider secondary indexes when you partition a database. You can even partition your secondary indexes.

### Partitioning by document
when you add a record to a partition, your database needs to add its key to the list of keys for a given secondary index (for example, when you add `{123: {color: red}}` to the database, add `{red: [123]}` to the `colors` index)
- **scatter/gather**: a query path for when each partition has its own secondary index. Your query needs to check every partition, then consult the local secondary index, then come back with the result.

### Partitioning by term
- **local index**: an index on a partition
- **global index**: is an index that covers all partitions. This must also be partitioned. Fast reads, slow writes
- **term partitioning**: when you partition an index based on its value (for instance, the index for all colors from A-M go in one partition)

## Rebalancing Partitions
When you move data from one node to another - reasons for doing this include:
- new nodes, node failures

requirements for rebalancing:
- datashould end up evenly balanced (duh)
- the database should be up during rebalancing
- move as little data as possible.

### What not to do
it can be tempting do use a modulo function to determine what record goes on which partition. If you have ten partitions, then you put `record 123` on `123%10 = 3` partition. This fails the moment you change the number of nodes you use. You'd have to reshuffle _everything_.

The solution is to have a fixed number of partitions. That way, you can shuffle the partitions around the node. You also need to balance the number of partitions you use
- fewer partitions = a less flexible setup
- more partitions = more overhead

### Dynamic Partitioning
some databases adjust the number of partitions dynamically. If the records in a partition get above a certain threshold, split that partition in two. If the records in a partition get below a certain threshold, merge it with another partition

### Partitioning proportionally to nodes
you can have a set number of partitions to nodes. This means that when you add a node, it spinds up a set number of partitions, then grabs records randomly from other nodes in your cluster

### Automatic or Manual Rebalancing
You have to find a happy medium between automatic and manual rebalancing. Manual rebalancing is quite time consuming. Automatic rebalancing can be dangerous (for example, if it coincides with a lot of traffic). It's best to have a human in the loop

## Request Routing
When you have multiple nodes, you need to figure out where to direct a query. The more general term for this problem is **service discovery**. You need something that makes routing decisions, and it _needs_ to be up to date. Here are three options
1. **round robin load balancer**
  - a client connect to any node in a cluster, randomly. If the record is in that node, serve it to the client. If it's in another node, forward the request to that one
  - nodes use a gossip prototol do disseminate changes in the cluster state
  - downside: this is a complex solution
2. **Routing Tier** (like Zookeeper)
  - have a partition awar load balander
  - this load balancer doesn't have any records on it. It just forwards traffic to the correct node
3. just require clients to be aware of partition executions

### Parallel Query Execution
- **MMP - Massive Parallel Processing**: more sophisticated queries (join, filter, group, aggregate...)
- **MMP query optimizer**: breaks down complex queries into a set of steps