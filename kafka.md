SEND MESSAGE API
KafkaProducer + ProducerRecord

BUILD STREAM API
KafkaStream + StreamBuilder + KafkaTable + KStream

存储到topic的数据可以被压缩，配置命令：
bin/kafka-topics.sh --create \
    --bootstrap-server localhost:9092 \
    --replication-factor 1 \
    --partitions 1 \
    --topic streams-wordcount-output \
    --config cleanup.policy=compact

DEMO STREAM APPLICATION
    bin/zookeeper-server-start.sh config/zookeeper.properties
    bin/kafka-server-start.sh config/server.properties
    bin/kafka-topics.sh --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic streams-plaintext-input
    bin/kafka-topics.sh --create \
        --bootstrap-server localhost:9092 \
        --replication-factor 1 \
        --partitions 1 \
        --topic streams-wordcount-output \
        --config cleanup.policy=compact
    bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe
    bin/kafka-run-class.sh org.apache.kafka.streams.examples.wordcount.WordCountDemo
    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic streams-plaintext-input
    bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
        --topic streams-wordcount-output \
        --from-beginning \
        --formatter kafka.tools.DefaultMessageFormatter \
        --property print.key=true \
        --property print.value=true \
        --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
        --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer

KAFKA STREAM SCRATCH
mvn archetype:generate \
    -DarchetypeGroupId=org.apache.kafka \
    -DarchetypeArtifactId=streams-quickstart-java \
    -DarchetypeVersion=2.2.0 \
    -DgroupId=streams.examples \
    -DartifactId=streams.examples \
    -Dversion=0.1 \
    -Dpackage=myapps

Topology topology = streamBuilder.build();
System.out.println(topology.describe());
Sub-topologies:
  Sub-topology: 0
    Source: KSTREAM-SOURCE-0000000000(topics: streams-plaintext-input) --> KSTREAM-FLATMAPVALUES-0000000001
    Processor: KSTREAM-FLATMAPVALUES-0000000001(stores: []) --> KSTREAM-SINK-0000000002 <-- KSTREAM-SOURCE-0000000000
    Sink: KSTREAM-SINK-0000000002(topic: streams-linesplit-output) <-- KSTREAM-FLATMAPVALUES-0000000001
  Global Stores:
    none

将 --> 翻译成 children: <-- 翻译成 parent: 便于阅读

server.properties文件中设置log.retention.hours=168控制log保存时间。
kafka stores its log file in a format of "logDir/topic-partition

kafka重要概念
EventTime 数据生成时候的初始时间
Window 在多长的时间段内对数据做聚合或者合并操作

