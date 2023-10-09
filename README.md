# System Design Resources
Mostly key concepts taken from Design Data Intensive Application book for now.

Index
--------

[Part I. Foundations of Data Systems](#part-1)
* [Reliable, Scalable, and Maintainable Applications](#part-1.1)
* [Data Models and Query Languages](#part-1.2)

### Part I. Foundations of Data Systems <a name="part-1"></a>
#### Chapter 1. Reliable, Scalable, and Maintainable Applications <a name="part-1.1"></a>
* Reliability means making systems work correctly, even when faults occur aka *fault-tolerant*.
* Scalability means having strategies for keeping performance good, even when load increases.
  * Percentiles are used to describe performance, usually abbreviated as p95, p99, p999, etc.
    * pN: response time threshold where N% of requests were faster, and (100-N)% were slower.
  * Approaches for coping with load
    * *Vertical scaling*: switching to a more powerful machine.
    * *Horizontal scaling*: distributing load across smaller machines.
    * *Elastic* system can automatically add computing resources when there is a load increase.
* Maintainability means making life better for eng and ops folks to work on the system.
  * Operability: Easy for ops to keep the system running smoothly.
  * Simplicity: Easy for new eng to understand the system.
  * Evolvability: Easy for eng to make changes to the system in the future.
 
#### Chapter 2. Data Models and Query Languages <a name="part-1.1"></a>
| Database   | Pros                                                                                             | Cons                                    |
|:-----------|:---------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------|
| Relational | Joins support for many-to-one / many-to-many relationships.<br/> Enforcement of records with same structure.   | Heterogenous structure.                                   |
| Document   | Tree-like structure (1-to-many relationships).<br/> Schema flexibility and better performance due to locality. | Deeply nested structure.<br/> Many-to-many relationships. |
| Graph      | Interconnected data with many-to-many relationships.<br/> Homogeneous data.                                    | N/A                                                       |

### Part II. Distributed Data <a name="part-2"></a>
* Motivation for distributing database across machines
  * Scalability: handle increasing load by spreading it across machines.
  * High availability: if one machine goes down, the other can take over.
  * Latency: data center are placed geographically closer to users.
* There are 2 ways to distribute a database
  * **Replication**: keeping a copy of the same data on several different machines.
  * **Partitioning** aka sharding: splitting a big database into smaller subsets called *partitions*.

#### Chapter 5. Replication <a name="part-2.1"></a>
* Algorithms for maintaining replicas
  * **Single-leader replication**:
  <br/> One node is the leader, all writes go to the leader, and the leader sends data to all the followers.
  ![Single-leader replication](diagrams/single-leader-replication.png)
   *Note: Client can read from leader / followers.*
  * **Multi-leader replication**: To be added.
  * **Leaderless replication**: To be added.
* Handling Node Outages
  * Follower failure: Follower retains the last transaction that was processed before crash, and request leader for data changes during downtime.
  * Leader failure: One of the followers is promoted to the new leader, and all other followers are reconfigured to replicate from the new leader.
* Handling Replication Lag
  * Clients may read stale data from an async follower that hasn't catched up with the leader. The inconsistency goes away eventually, hence it's called *eventual consistency*.
  * Problems:
    * Users see stale data (e.g. data changes from write request wasn't reflected). 
   ![Replication lag issue #1](diagrams/replication-lag-issue-1.png)
    Approach: Requires *read-after-write consistency*, which guarantees that after user reloads the page, they will see the data they just submitted.
    <br/>How?
      * Read from leader based on some conditions (e.g. read user's own profile picture from leader, otherwise read from follower).
    * Users see latest data on 1st read, but see old data on 2nd read
    ![Replication lag issue #2](diagrams/replication-lag-issue-2.png)
    Approach: Requires *monotonic reads*, which guarantees that after user won't see old data after seeing new data.
    <br/>How?
      * Each user make their reads from same replica. 
    * Users see answer before question (e.g. observer see answer from user 1 before question from user 2)
    ![Replication lag issue #3](diagrams/replication-lag-issue-3.png)
    Approach: Requires *consistency prefix reads*, which guarantees that if sequence of writes happens in some order, then client will sees those writes in the same order.
    <br/>How?
      * Make writes that are casually related to each other to the same partition.