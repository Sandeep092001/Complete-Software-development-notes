# Kafka Partitions and Consumer Groups

## Partitions and ordering

Partitions provide horizontal parallelism. Kafka guarantees record order within
a partition, not one global order across the topic.

When per-order ordering matters, publish events with a stable key such as
`orderId`:

```text
Order 101 CREATED → PAID → SHIPPED
          same key → same partition (under a stable partitioning setup)
```

A hot key can overload one partition. Adding partitions may change key mapping,
so designs should not assume permanent historical partition placement.

## Two different assignments

- Producer partitioner: record/key → partition.
- Consumer-group coordinator/assignor: partition → consumer.

Confusing these is a common interview mistake.

## Consumer groups

Within a group, each partition is assigned to at most one active consumer at a
time. Therefore four partitions support at most four actively assigned consumers
in that group; with eight consumers, at least four are idle. A different group
can independently read all records.

## Rebalancing

Membership or partition/subscription changes cause ownership reassignment. If a
consumer crashes and stops heartbeating, its partitions move to surviving
members, which resume from committed offsets. Rebalances may pause processing
and expose duplicate work if offsets lag completed side effects.

## Interview traps

- More consumers than partitions do not increase active group parallelism.
- More partitions improve throughput but add metadata, files, and rebalance cost.
- Ordering is per partition, not per topic or consumer group.
- Same key routing depends on partition count and partitioning strategy.
- Rebalancing is not failure-free or cost-free.

## Concise interview answer

The producer chooses a partition, normally using the key, while the group
assignor gives whole partitions to consumers. Partition count bounds active
parallelism per group. On failure a rebalance transfers ownership, and processing
resumes from committed offsets, so consumers must tolerate replay.

## Quick revision

- Key related events by the ordering boundary.
- One partition has one consumer owner per group.
- Different groups read independently.
- Consumer count above partition count creates idle members.
- Failure triggers reassignment from committed progress.
