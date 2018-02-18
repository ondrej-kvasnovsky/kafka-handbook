# Installation

Kafka needs Zookeer in order to run.

```
➜ brew install zookeeper
➜ brew services start zookeper
```

We can instal and run Kafka the same way.

```
➜ brew install kafka
➜ brew services start kafka
```

When both are started, we can validate what all services are running on our system.

```
➜ brew services list
Name           Status  User   Plist
zookeeper      started ondrej /Users/ondrej/Library/LaunchAgents/homebrew.mxcl.zookeeper.plist
kafka          started ondrej /Users/ondrej/Library/LaunchAgents/homebrew.mxcl.kafka.plist
```

Now we need to find where is Kafka installed so we can access its tools.

```
➜ brew --prefix kafka
/usr/local/opt/kafka
```

Add it to your profile, for example, if you use zsh, add this line into your `vi ~/.zshrc`.

```
# Kafka
export PATH=/usr/local/opt/kafka/bin:$PATH
```

Don't forget to reload the config: `source ~/.zshrc`

Now we can try out whether we can access Kafka commands. Lets try to list all topics that are present in Kafka.

```
➜ kafka-topics --list --zookeeper localhost:2181
__consumer_offsets
```

### Setup multi-broker cluster

We have managed to run single broker. Lets see how to configure and start up multiple Kafka brokers.

```
➜  cp /usr/local/etc/kafka/server.properties broker1.properties
➜  cp /usr/local/etc/kafka/server.properties broker2.properties
```

Because we are going to run brokers on the same machine, we need to change these in `broker1.properties`

```
broker.id=1
listeners=PLAINTEXT://:9093
log.dirs=/usr/local/var/lib/kafka-logs-1
```

And these in `broker2.properties`

```
broker.id=2
listeners=PLAINTEXT://:9094
log.dirs=/usr/local/var/lib/kafka-logs-2
```

Now we can start up the two brokers we made the configuration for.

```
➜ /usr/local/opt/kafka/bin/kafka-server-start broker1.properties &
➜ /usr/local/opt/kafka/bin/kafka-server-start broker2.properties &
```

We can create topic that will be using 3 brokers we have started up so far.

```
➜ kafka-topics --create \
    --zookeeper localhost:2181 \
    --replication-factor 3 \
    --partitions 1 \
    --topic my-replicated-topic
Created topic "my-replicated-topic".
```

We can see what is happening in the cluster using `describe topics` command.

```
➜ kafka-topics --describe \
--zookeeper localhost:2181 \
--topic my-replicated-topic
Topic:my-replicated-topic    PartitionCount:1    ReplicationFactor:3    Configs:
    Topic: my-replicated-topic    Partition: 0    Leader: 1    Replicas: 1,2,0    Isr: 1,2,0
```

* leader is node that is responsible for all reads and writes for given partition, if we would set partitions to 2, we would see two lines here
* replicas - list of all nodes that replicate the log for this partition
* isr - in-sync replicas, or "alive" replicas

If we want to kill the nodes we created, we first get the process id and then kill it.

```
➜ ps aux | grep broker1.properties
ondrej           66855   0.0  1.8......
....
➜ kill -9 66855
```

Now we can check what --describe command returns when we run it again, after we have killed one broker.

```
➜ kafka-topics --describe \
--zookeeper localhost:2181 \
--topic my-replicated-topic
Topic:my-replicated-topic    PartitionCount:1    ReplicationFactor:3    Configs:
    Topic: my-replicated-topic    Partition: 0    Leader: 2    Replicas: 1,2,0    Isr: 2,0
```



