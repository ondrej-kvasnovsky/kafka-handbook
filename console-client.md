# Console Client

We are going to look how to use Kafka from command line. 

### Create topic

The first thing we need to do is to create a topic.

```
➜ kafka-topics --create \
  --zookeeper localhost:2181 \
  --replication-factor 1 \
  --partitions 12 \
  --topic my-topic

Created topic "my-topic".
```

### List topics

When there are existing topics in Kafka, we can list them. 

```
➜ kafka-topics --list --zookeeper localhost:2181

__consumer_offsets
my-topic
```

### Produce messages

We are going to send couple of messages into Kafka. 

```
➜ kafka-console-producer \
    --broker-list localhost:9092 \
    --topic my-topic
>my first message
>
```

### Consume messages

Now we need to start consumer that will read messages from the topic.

```
➜ kafka-console-consumer \
    --bootstrap-server localhost:9092 \
    --topic my-topic \
    --from-beginning
my first message
```



