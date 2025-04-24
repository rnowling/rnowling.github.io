---
layout: post
title:  "SQL Schema for Durable Event Processing"
date:   2025-04-23 01:13:19
categories: "distributed-systems"
tags: ["dise-project"]
---

In my [ML Production Systems](https://rnowling.github.io/dise-project/) class, students implement a system that trains, evaluates,
and deploys ML models based on continuously-ingested data. Earlier versions of the class used
[Apache Kafka](https://kafka.apache.org/) for streaming data, [MongoDB](https://www.mongodb.com/) for enrichment data accessed by key,
and [MinIO](https://min.io/) for storing models and data for batch processing.  The complexity of this solution was prohibitive to
both students (too many technologies to master in a single semester) and me as the instructor (spent too much time working with
students to debug their installations).  This year, I replaced all three services with [PostgreSQL](https://www.postgresql.org/),
the Swiss army knife of data storage solutions.

The students are given a simulator that continuously generates events.  As part of the system, they write several pipelines that use
those events for tasks such as generating cleaned and joined data for model training and online evaluation and monitoring of deployed
models.  The events are stored in a raw events table queried by the pipelines.  I wanted to use PostgreSQL to implement a durable message
queue that supports multiple consumers with independent processing states for each event.  In other words, I wanted to implement
Apache Kafka-like functionality.

PostgreSQL can certainly be used as a message queue, including properly handling at-most-once delivery semantics with multiple competing
consumers with the [`FOR UPDATE SKIP LOCKED`](https://adriano.fyi/posts/2023-09-24-choose-postgres-queue-technology) functionality.
The message queue pattern, however, only allows each message to be read once.  I wanted events to be delivered to multiple consumers
(similar to the [pub-sub pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)).  Unlike the pub-sub pattern
which only delivers messages to currently-connected consumers, I wanted messages to be persisted and replayable when new consumers
come online.

I had a two additional requirements. I didn't want the producer to be aware of the consumers, so that consumers could be added and removed
over time.  This requirement ruled out a solution such as having the producer write events to a separate table for each consumer. My other
requirement was that the insertion and retrieval of events was efficient in terms of algorithmic complexity.

After considering multiple solutions, I came up with the following approach.

1. A single raw events table that contains a unique identifer, timestamp, and JSON payload for each event.  The table is indexed by the
   the identifier.
1. For each consumer:
   1. A table that tracks the processing status of each event by that consumer.  The table's columns include the event identifier,
      the event timestamp, and a boolean indicating whether the event has been processed.  The table will have an index on the
      date and status.
   1. A trigger that inserts a record into the events processed states table whenever a new event inserted into the raw events table.

The consumer can process a single or batch of unprocessed events by:

1. Beginning a transaction
1. Identifying unprocessed event ids with:
   ```sql
   SELECT event_id FROM event-processing-states-consumerABC
   FOR UPDATE SKIP LOCKED
   WHERE event_date >= INTERVAL_BEGIN
     AND event_date <= INTERVAL_END
     AND NOT event_processed
   ORDER BY event_date DESC
   LIMIT BATCH_SIZE;
   ```
1. For each event:
   1. Process the event
   1. Update the event's status:
      ```sql
      UPDATE event-processing-states-consumerABC
         SET event_processed = true
       WHERE event_id = ID;
      ```
1. Committing the transaction
