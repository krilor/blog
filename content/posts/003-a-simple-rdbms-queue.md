+++ 
draft = false
date = 2019-02-03T11:01:33+01:00
title = "A simple RDBMS queue pattern"
description = "How to set up a queue in PostgreSQL and benefit from the ACID properties."
slug = "simple-rdbms-queue-pattern" 
tags = ['postgres','db','sql','pattern']
categories = ['poc']
externalLink = ""
+++

I recently attended a [MeetUp with Axel Fontaine](https://www.meetup.com/Oslo-Software-Architecture/events/257922639/) where he briefly threw out an example of how to create a queue in an relational database. In this post I have a closer look at that idea and test it out in [PostgreSQL](https://www.postgresql.org/).

So this is the gist of the idea. A consumer would consume messages while placing locks on the rows being processed, leveraging the ACID properties of the database.

```sql
BEGIN;
DELETE FROM queue
WHERE id = (
    select id from queue ORDER BY id LIMIT 1
    FOR UPDATE -- lock the row
    SKIP LOCKED -- skip locked rows
RETURNING message;

-- Do stuff

COMMIT; -- Once responsibility of the message has been taken
-- or ROLLBACK if something dies or goes south.
```

The queue table would look something like this:

ID | MESSAGE
---|------
1  | MESSAGE_ONE
2  | MESSAGE_TWO

Of course, this would be more helpful if the messages where an actual (JSON) message, but this serves as an example.

So, one part of the system would create messages and put them on the queue and another would consume from the queue. There are tons of use cases for such functionality, and that is probably why there are so many queue solutions out there.

The idea here, however, is that you probably allready have a database, so why not use it? KISS!

Let's use docker to spin up a new [Postgres database using docker](https://hub.docker.com/_/postgres) and connect to it.

```bash
docker run  --name queue-db -p 5432:5432 -d postgres:alpine
docker exec -it queue-db bash
psql -U postgres
```

{{<todo>}}
DROP FUNCTION log_queue_message CASCADE;
DROP TABLE queue;
DROP TABLE queue_log;
{{</todo>}}


At this point we are connected to the DB and can specify what we need. I'll create a `queue` table for the queue and a `queue_log` table to keep track of processed messages and when they have been processed.

```sql
\l

CREATE DATABASE queue;

\c queue

CREATE TABLE queue (
    id SERIAL NOT NULL PRIMARY KEY,
    message VARCHAR(16)
);

CREATE TABLE queue_log (
    id INTEGER NOT NULL,
    message VARCHAR(16),
    processed TIMESTAMP
);

\dt

CREATE FUNCTION log_queue_message() RETURNS trigger AS $log_queue_message$
    BEGIN
        INSERT INTO queue_log VALUES ( OLD.*, CURRENT_TIMESTAMP );
        RETURN OLD;
    END;
$log_queue_message$ LANGUAGE plpgsql VOLATILE;

CREATE TRIGGER trg_queue_log AFTER DELETE ON queue
    FOR EACH ROW EXECUTE FUNCTION log_queue_message();

```
Then a message producer would insert messages like so.

```sql
INSERT INTO queue (message) values ('MESSAGE_ONE');
INSERT INTO queue (message) values ('MESSAGE_TWO');
INSERT INTO queue (message) values ('MESSAGE_THREE');
INSERT INTO queue (message) values ('MESSAGE_FOUR');
```
Consumers would use the pattern outlined on the top of the post.

## How it fits together

First of all, consumers and producers can `INSERT` and `DELETE` in `queue` without caring about keeping track of what is going on, just `COMMIT` then they are done.

{{<mermaid>}}
graph LR
    Q[queue]
    C1((Consumer))
    C2((Consumer))
    C3((Consumer))
    P1((Producer))
    P2((Producer))
    P1 -- Insert --> Q
    P2 -- Insert --> Q
    Q -- Consume/Delete --> C1
    Q -- Consume/Delete --> C2
    Q -- Consume/Delete --> C3
{{</mermaid>}}

At the same time, the function, trigger and `queue_log` table will ensure that we keep track of which messages has been consumed.

{{<mermaid>}}
graph LR
    Q[queue]
    L[queue_log]
    T(trg_queue_log)
    F(log_queue_message)
    Q -- Trigger on DELETE --> T
    T -- Execute --> F
    F -- Insert with timestamp --> L
{{</mermaid>}}

## Summary

Using a RDBMS for queueing is maybe not the most celebrated pattern in software achitecture. In fact, it's actually a bit contested. I agree that one should evaluate using purpose-built technologies to handle message queueing, but in a situation where you allready have a database in your app and you want to keep things simple, then this pattern could be an easy (and pretty decent) way to add a queue.