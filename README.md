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
    * pN: response threshold where N% of requests were faster, and (100-N)% were slower.
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