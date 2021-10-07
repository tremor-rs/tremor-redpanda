# Redpanda delivery guarantees

This example goes into how tremors delivery guarantees work in combination with a sink and source that also support delivery guarantees.

The scenario isn't all-encompassing but looks at the following specific use-case:

1. A tremor source that does not hold guarantees.
2. A WAL to persist data from the source
3. A Redpanda instance were data is sent to
4. A secondary tremor instance with a Redpanda source that reads the data and prints it to stdout

With this we can demonstrate the recovery and delivery guarantees using a WAL with Redpanda.

We can start the example using `docker-compose up`. Then with `docker ps` we find the Kafka instance and can introduce an artificial error using `docker pause <container>`. We will see the messages stopping if we wait for a while we can reenable kafka with `docker unpause <container>` and will see the message flow resuming with a number of duplicated but no lost messages.

## Build the minimal test client docker image

This is a pre-requisite step

```bash
$ cd docker/metadata-cli
$ docker build -t metadata-cli .
```

## Verifying tremor is publishing to redpanda

```bash
$ docker-compose exec redpanda rpk topic consume tremor
```

## Issue

Currently the `tremor-out` container doesn't always succesfully
subscribe to the `tremor` topic. On rare occasions it runs ok.

A minimal rust librdkafka client is also run ( the `metadata` service )
under docker to attempt to reproduce but without the overhead of tremor
as it should be easier to debug with a minimal client
