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











