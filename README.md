# Start it up

`dkc up -d`

Check the [quickstart](https://kafka.apache.org/quickstart) and the [doc](https://kafka.apache.org/documentation/).

# Command line playground

`dkc exec kafka bash`

>  `cd /opt/kafka/bin`

>  `./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic test`
>  `./kafka-console-producer.sh --broker-list kafka:9092 --topic test`

# Java playground

Check de [producer doc](https://kafka.apache.org/20/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html) and [consumer doc](https://kafka.apache.org/20/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html).

```
    @Test
    public void test() throws ExecutionException, InterruptedException {
//        produce();
        consume();
    }

    private void produce() throws ExecutionException, InterruptedException {
        Properties props = new Properties();
        props.put("bootstrap.servers", "10.0.1.124:9092");
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        Producer<String, String> producer = new KafkaProducer<>(props);
        for (int i = 0; i < 100; i++) {
            Future<RecordMetadata> result =  producer.send(new ProducerRecord<String, String>("test", Integer.toString(i), Integer.toString(i)));
            System.out.println(result.get());
        }

        producer.close();
    }

    private void consume() {
        Properties props = new Properties();
        props.put("bootstrap.servers", "10.0.1.124:9092");
        props.put("group.id", UUID.randomUUID().toString());
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Arrays.asList("test"));
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(2000));
            System.out.println(records.count());
            for (ConsumerRecord<String, String> record : records)
                System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
        }
    }
```
