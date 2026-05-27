# High Level Design (HLD) — Lecture 2

# CAP Theorem

CAP Theorem is one of the most important concepts in Distributed Systems.

It is heavily used in:
- Distributed databases
- System design interviews
- Large scale backend systems

---

# What is CAP Theorem?

CAP Theorem states that:

> In a distributed system with replicated data, it is impossible to guarantee all 3 properties together at the same time.

The 3 properties are:

1. Consistency (C)
2. Availability (A)
3. Partition Tolerance (P)

---

# CAP Theorem Diagram

```text
                CAP Theorem
                     /|\
                    / | \
                   /  |  \
                  /   |   \
                 C    A    P
```

---

# Important Rule

A distributed system can guarantee only:

```text
Any 2 out of these 3:
- Consistency
- Availability
- Partition Tolerance
```

---

# 1. Consistency (C)

Consistency means:

> All database nodes should contain the same data at the same time.

When data is updated:
- Every node should return the latest updated value.

---

# Example of Consistency

Suppose:

```text
Node A = 5
Node B = 5
Node C = 5
```

Now if value becomes:

```text
Value = 10
```

Then all nodes should immediately have:

```text
Node A = 10
Node B = 10
Node C = 10
```

If any node still returns old value:
- System is inconsistent.

---

# Consistency Diagram

```text
        [Node A]
           |
           |
[Node B]---|---[Node C]

All nodes contain same data
```

---

# 2. Availability (A)

Availability means:

> Every request should receive a response.

The response can be:
- Success
- Failure

But system should always respond.

---

# Example of Availability

```text
Client -----> Database Node
              |
              |----> Response Returned
```

Even if some nodes fail:
- Remaining nodes should still respond.

---

# 3. Partition Tolerance (P)

Partition Tolerance means:

> System should continue working even if communication between nodes breaks.

This communication failure is called a:

```text
Network Partition
```

---

# Example of Partition

```text
        [Node A]

           X   <- Connection Broken

[Node B]---------[Node C]
```

Even after network failure:
- System should still operate.

---

# Real Meaning of Partition Tolerance

In distributed systems:
- Network failures are unavoidable
- Machines can disconnect
- Packets can be lost

So modern distributed systems almost always require:
- Partition Tolerance

---

# Why CAP Tradeoff Happens

During network partition:

You must choose between:

1. Consistency
OR
2. Availability

Because both cannot be guaranteed together.

---

# CP System (Consistency + Partition Tolerance)

CP systems prioritize:
- Consistency
- Partition tolerance

They may sacrifice:
- Availability

---

# CP Example

If nodes cannot synchronize properly:

```text
System may reject requests
instead of returning stale data
```

Reason:
- Maintaining consistency is more important.

---

# CP Diagram

```text
Partition Happens
        |
        v

System chooses:
- Correct data
- Possible downtime
```

---

# AP System (Availability + Partition Tolerance)

AP systems prioritize:
- Availability
- Partition tolerance

They may sacrifice:
- Strong consistency

---

# AP Example

Even during partition:

```text
System still responds
but some nodes may return old data
```

Reason:
- Availability is more important.

---

# AP Diagram

```text
Partition Happens
        |
        v

System chooses:
- Always respond
- Eventual consistency
```

---

# CA System (Consistency + Availability)

CA systems prioritize:
- Consistency
- Availability

But they do NOT tolerate partitions.

---

# Problem with CA Systems

In real distributed systems:
- Network failures always happen

So pure CA systems are very rare in practice.

---

# CAP Theorem Summary

| Property | Meaning |
|---|---|
| Consistency | All nodes contain same data |
| Availability | Every request gets response |
| Partition Tolerance | System works despite network failures |

---

# CP vs AP

| Feature | CP System | AP System |
|---|---|---|
| Priority | Consistency | Availability |
| During Partition | May reject requests | Always responds |
| Data Accuracy | Strong consistency | Eventual consistency |
| Availability | Reduced | High |

---

# Eventual Consistency

In AP systems:

```text
Data may be temporarily inconsistent
but eventually becomes consistent.
```

Example:
- Social media likes
- View counts
- Distributed caches

---

# Important Interview Points

- CAP Theorem applies to distributed systems
- All 3 properties cannot be fully guaranteed together
- Partition tolerance is almost always required
- During partition:
  - Choose CP or AP
- CP sacrifices availability
- AP sacrifices immediate consistency

---

# Real World Examples

## CP Systems
- MongoDB (configured strongly consistent)
- HBase
- Zookeeper

---

## AP Systems
- Cassandra
- DynamoDB
- CouchDB

---

# Quick Revision

```text
CAP Theorem

C = Consistency
A = Availability
P = Partition Tolerance

Only 2 can be prioritized together.
```