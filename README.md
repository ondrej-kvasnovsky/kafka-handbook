# Kafka

Kafka is distributed streaming platform that stores data in logs, time-series based and append only format.

Kafka messages are written into topics \(messages written into a topic are stores in logs structure\). Then there can be multiple subscribers that read the data.

> [Very good explanation](https://techbeacon.com/what-apache-kafka-why-it-so-popular-should-you-use-it) of what Kafka is doing and how Kafka works.

Kafka is especially good for: 

* messaging, for example as replacement for RabbitMQ
* website activity tracking
* metrics
* log aggregation
* stream processing
* [event sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) - where app states are stored as events and we can reconstruct past states using those



