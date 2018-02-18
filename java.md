# Java Client

Lets create topic, publish couple of messages into the topic and then consume the messages.

### Producing messages

We produce 10 messages and send them to Kafka. 

```
import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.LongSerializer;
import org.apache.kafka.common.serialization.StringSerializer;

import java.util.Properties;
import java.util.concurrent.ExecutionException;

public class MyProducer {
    private final static String TOPIC = "my-topic";
    private final static String BOOTSTRAP_SERVERS = "localhost:9092,localhost:9093,localhost:9094";

    public static void main(String[] args) throws Exception {
        new MyProducer().runProducer(10);
    }

    public void runProducer(int sendMessageCount) throws Exception {
        try (Producer<Long, String> producer = createProducer()) {
            for (long index = 0; index < sendMessageCount; index++) {
                send(producer, index);
            }
        }
    }

    private void send(Producer<Long, String> producer, long index) throws InterruptedException, ExecutionException {
        ProducerRecord<Long, String> record = new ProducerRecord<>(TOPIC, index, "My message " + index);
        RecordMetadata metadata = producer.send(record).get();
        System.out.printf("sent record(key=%s value=%s) meta(partition=%d, offset=%d)\n",
                record.key(), record.value(), metadata.partition(),
                metadata.offset());
    }

    private Producer<Long, String> createProducer() {
        Properties props = new Properties();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        props.put(ProducerConfig.CLIENT_ID_CONFIG, "KafkaExampleProducer");
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, LongSerializer.class.getName());
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        return new KafkaProducer<>(props);
    }

}
```

### Consuming messages

This code starts new thread which reads messages that have been produced in last 1 second. 

```
import org.apache.kafka.clients.consumer.Consumer;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.serialization.LongDeserializer;
import org.apache.kafka.common.serialization.StringDeserializer;

import java.util.Collections;
import java.util.Properties;

public class MyConsumer {

    private final static String TOPIC = "my-topic";
    private final static String BOOTSTRAP_SERVERS = "localhost:9092,localhost:9093,localhost:9094";

    public static void main(String[] args) {
        Runnable consumer = () -> {
            while (true) {
                new MyConsumer().run(1000);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };
        new Thread(consumer).start();
    }

    public void run(int timeout) {
        Consumer<Long, String> consumer = createProducer();
        ConsumerRecords<Long, String> fromKafka = consumer.poll(timeout);
        System.out.println(fromKafka.count());
        fromKafka.forEach(it -> {
//            System.out.println(it.value());
        });
        consumer.commitAsync();
        consumer.close();
    }

    private Consumer<Long, String> createProducer() {
        Properties props = new Properties();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "KafkaExampleConsumer");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, LongDeserializer.class.getName());
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        KafkaConsumer<Long, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Collections.singleton(TOPIC));
        return consumer;
    }
}

```



