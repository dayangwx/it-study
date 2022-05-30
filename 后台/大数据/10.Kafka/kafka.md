# Kafka

---

## 1、Kafka 概述

### 1-1. 定义

​	Kafka 是一个分布式的基于发布/订阅模式的消息队列（Message Queue），主要应用于大数据实时处理领域

### 1-2. 消息队列

​	<img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/61af0f1a0791297f4da58cf5" style="zoom:50%;" />

#### 1-2-1. 为什么要使用消息队列

- 解耦

- 可恢复性

  > 系统的一部分组件失效时，不会影响整个系统。
  > 消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，
  > 加入队列中的消息仍然可以在系统恢复后被处理。

- 缓冲

- 灵活的峰值处理能力

  > 使用消息队列能够使关键组件顶住突发的访问压力，
  > 而不会因为突发的超负荷的请求而完全崩溃。

- 异步通信

#### 1-2-2.消息队列的两种形式

- 点对点

  > ​	一对一，消费者主动拉取数据，消息收到后消息清除。

  <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/%E7%82%B9%E5%AF%B9%E7%82%B9%E6%B6%88%E8%B4%B9.png" style="zoom: 67%;" />

  - 特点

    ==1：生产者生产消息发送到 Queue 中，然后消费者从 Queue 中取出并且消费消息。==
    ==2：消息被消费以后，Queue 中不再有存储，所以消费者不可能消费到已经被消费的消息。==
    ==3：Queue 支持存在多个消费者，但对于一个消息而言，只有一个消费者可以消费。==

- 发布/订阅

  > ​	一对多，消费者消费数据之后不会清除消息

  <img src="https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/%E5%8F%91%E5%B8%83-%E8%AE%A2%E9%98%85%E6%A8%A1%E5%BC%8F.png" style="zoom:67%;" />

  - 特点

    ==生产者将消息发布到 topic 中，同时有多个消息消费者（订阅）消费该消息。==
    ==和点对点方式不同，发布到 topic 中的消息会被所有订阅者消费。==

#### 1-2-3.Kafka基础架构

​	![](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/kafka%E6%9E%B6%E6%9E%84.png)

- Producer

  > 消息生产者，就是向 Kafka broker 发消息的客户端。

- Consumer

  > 消息消费者，向 Kafka broker 取消息的客户端。

- Consumer Group（CG）

  > 消费者组，由多个 Consumer 组成。
  > 消费者组内每个消费者负责消费不同分区的数据，
  > 一个分区只能由一个组内消费者消费；消费者组间互不影响。
  > 所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。
  >
  > **每个消费者从不同的分区的leader中取数据**

- Broker

  > 一台 Kafka 服务器就是一个 broker。**一个集群由多个 broker 组成。**一个 broker 可以容纳多个 topic。

- Topic

  > 可以理解为一个队列[queue]，生产者和消费者面向的都是一个 topic。

- Partition

  > 一个非常大的 topic 可以分布到多个 broker（即服务器）上，
  > 每个broker上又有多个 Partition，
  > 每个 partition 都是一个有序的队列。
  >
  > 
  >
  > 理解：
  >
  > 一个topic分布到多个broker上，
  > 每个broker上又有多个paritoin。
  > 在架构图中：
  >  topicA = Broker1+Broker2
  >  Broker1 = Partiton0+Partition1
  >  Broker2 = Partition0+Partition1
  > 所以相当于topicA是由4个有序队列[queue]组成
  > Broker1中的Partition0为leader，
  > Broker2中的Partition0为follower
  > follower中的数据从leader中同步而来
  > 所以leader和follower中存的数据是一样的。
  > 理论上讲：partition0和partition1里的数据不一样，但他们两加起来就等于topicA的所有数据了。

- Replication

  > 副本，为保证集群中某个节点发生故障时，该节点上的 partition 数据不丢失，且 Kafka 仍然可以继续工作
  > Kafka 提供了副本机制，一个 topic 的每个分区都有 【若干个副本+一个 leader +若干个 follower】

- leader

  > 每个分区(partition)多个副本的 ”主“，
  > 生产者发送数据的对象，
  > 以及消费者消费数据时的对象都是 leader。

- follower

  > 每个分区多个副本的 “从”，
  > 实时从 leader 中同步数据，保持和 leader 数据的同步。
  > leader 发生故障时，某个 follower 会成为新的 leader。

## 2、Kafka安装部署

### 2-1.单机



### 2-2.集群



## 3、Kafka命令行操作

### 3-1.启动

- `后台常驻方式，带上参数 -daemon，如：`

  ```
  kafka-server-start.sh -daemon /opt/hadoop/kafka/cluster/kafka-3.0/config/server.properties
  ```

- `指定 JMX port 端口启动，指定 jmx，可以方便监控 Kafka 集群` 

  ```bash
  JMX_PORT=9991 kafka-server-start.sh -daemon /usr/local/kafka/config/server.properties
  ```

### 3-2.停止

```bash
kafka-server-stop.sh
```

### 3-3.状态

```bash
jps
```

### 4、Topic

#### 4-1.创建 Topic

​			`参数 --topic 指定 Topic 名，--partitions 指定分区数，--replication-factor 指定备份数`

```bash
kafka-topics.sh --create --bootstrap-server tech1.xiu.com:9092 --replication-factor 1 --partitions 1 --topic test1
```

​			`注意，如果配置文件 server.properties 指定了 Kafka 在 zookeeper 上的目录，则参数也要指定，否则会报无可用的 brokers（下面部分命令也有同样的情况），如：`

```bash
/usr/local/kafka/bin/kafka-topics.sh --create --zookeeper localhost:2181/kafka --replication-factor 1 --partitions 1 --topic test
```

#### 4-2.列出所有 Topic

```bash
kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092

kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092,tech2.xiu.com:9092,tech3.xiu.com:9092
```

#### 4-3.生产数据

```bash
kafka-console-producer.sh --broker-list tech1.xiu.com:9092 --topic test
```

#### 4-4.查看数据

```bash
kafka-console-consumer.sh --bootstrap-server tech2.xiu.com:9092  --topic test --from-beginning
```

#### 4-5.列出所有topic

```bash
kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092,tech2.xiu.com:9092,tech3.xiu.com:9092 
```

#### 4-6.群起群关脚本

> kk.start kk.stop

```bash
#!/bin/bash

    case $1 in
    "start"){
      for i in tech1.xiu.com tech2.xiu.com tech3.xiu.com
        do
          echo "****************** $i *********************"
          ssh $i "source /etc/profile && kafka-server-start.sh -daemon /opt/hadoop/kafka/cluster/kafka-3.0/config/server.properties"
        done
    };;

    "stop"){
      for i in tech1.xiu.com tech2.xiu.com tech3.xiu.com
        do
          echo "****************** $i *********************"
          ssh $i "kafka-server-stop.sh"
        done
    };;

    esac
```

## 5、架构与应用

![](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521214623094.png)

![image-20220521210504379](https://raw.githubusercontent.com/dayangwx/cloudimg/master/img/image-20220521210504379.png)

![image-20220521210529962](D:\liuwx\it study\后台\大数据\kafka\kafka.assets\image-20220521210529962.png)

![image-20220522084325625](D:\liuwx\it study\后台\大数据\kafka\kafka.assets\image-20220522084325625.png)

![image-20220522084518718](D:\liuwx\it study\后台\大数据\kafka\kafka.assets\image-20220522084518718.png)

> 什么是幂等性？
>
> 所谓幂等性，就是同一个操作的多次执行，得到的结果不变，并且不会产生其他副作用。（例如一条消息被反复提交，最终只能有一条提交被持久化。）

**原理：**

​	首先Broker会给每个Producer一个ID(又称PID)，并且Producer发送的每一条消息，都要有一个序列号（这个序列号就是区分存放信息的关键），这些消息过来就有了三个部分：<Producer ID, 分区号, 消息序列号>,一旦三者同时重复，就可以认为是重复的消息，Leader持久化以后就会覆盖原先的提交。

那么就有局限性了：

    Producer故障重启后，重新分配的Producer ID不同了，即使是消息重复也检查不到了
    分区之间的（partition_1 和 partition_2）中重复消息无法检测

所以，幂等性仅能保证单个生产者会话中单个分区的消息不重复


```java
package com.neu.producer;

import org.apache.kafka.clients.producer.*;

import java.util.Properties;

public class CallBackProducer {
    public static void main(String[] args) {

        // 1.创建配置信息
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "slave1:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");

        // 2.创建生产者对象
        KafkaProducer<String, String> producer = new KafkaProducer<String, String>(properties);

        // 3.发送数据
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<>("demo", "hello--" + i), (metadata, exception) -> {
                if (exception == null) {
                    System.out.println(metadata.partition() + "--" + metadata.offset());
                } else {
                    exception.printStackTrace();
                }
            });
        }

//        for (int i = 0; i < 10; i++) {
//            producer.send(new ProducerRecord<>("demo", "hello--" + i), new Callback() {
//                @Override
//                public void onCompletion(RecordMetadata metadata, Exception exception) {
//                    if (exception == null) {
//                        System.out.println(metadata.partition() + "--" + metadata.offset());
//                    } else {
//                        exception.printStackTrace();
//                    }
//                }
//            });
//        }

        // 4.关闭资源
        producer.close();
    }
}

```

自定义分区器：

```java
package com.neu.partitioner;

import org.apache.kafka.clients.producer.Partitioner;
import org.apache.kafka.common.Cluster;

import java.util.Map;

public class MyPartitioner implements Partitioner {
    @Override
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        return 1;
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> configs) {

    }
}

```

```java
package com.neu.producer;

import org.apache.kafka.clients.producer.*;

import java.util.Properties;

public class PartitionProducer {
    public static void main(String[] args) {

        // 1.创建配置信息
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "slave1:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        // 添加分区器
        properties.put(ProducerConfig.PARTITIONER_CLASS_CONFIG, "com.neu.partitioner.MyPartitioner");

        // 2.创建生产者对象
        KafkaProducer<String, String> producer = new KafkaProducer<String, String>(properties);

        // 3.发送数据
        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<>("demo", "hello--" + i), new Callback() {
                @Override
                public void onCompletion(RecordMetadata metadata, Exception exception) {
                    if (exception == null) {
                        System.out.println(metadata.partition() + "--" + metadata.offset());
                    } else {
                        exception.printStackTrace();
                    }
                }
            });
        }

        // 4.关闭资源
        producer.close();
    }
}
```

同步发送的意思是，一条消息发送后，会阻塞当前线程，直至返回 ack。由于 send 方法返回的是一个 Future 对象，根据 Future 对象的特点，我们也可以实现同步发送的效果，只需在调用 Future 对象的 get 方法即可。

```java
// 10.同步发送数据
for (int i = 0; i < 10; i++) {
 	producer.send(new ProducerRecord<>("demo", "neu", "hello--" + i)).get();
}

//        for (int i = 0; i < 10; i++) {
//            Future<RecordMetadata> demo = producer.send(new ProducerRecord<>("demo", "neu", "hello--" + i));
//            RecordMetadata recordMetadata = demo.get();
//        }

```



Consumer



```java
package com.neu.consumer;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Collections;
import java.util.Properties;

public class MyConsumer {

    public static void main(String[] args) {

        // 1.创建消费者配置信息
        Properties properties = new Properties();

        // 2.给配置信息赋值
        // 连接的集群
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "slave1:9092");
        // 开启自动提交
        properties.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, true);
        // 自动提交的延时
        properties.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
        // key,value的反序列化
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");
        // 消费者组
        properties.put(ConsumerConfig.GROUP_ID_CONFIG, "bigData");

        // 3.创建消费者
        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(properties);

        // 4.订阅主题
        //consumer.subscribe(Collections.singletonList("demo"));
        consumer.subscribe(Arrays.asList("demo", "first"));

        // 5.获取数据
        while (true) {
            ConsumerRecords<String, String> consumerRecords = consumer.poll(100);

            // 解析并打印consumerRecords
            for (ConsumerRecord<String, String> consumerRecord : consumerRecords) {
                System.out.println(consumerRecord.key() + "--" + consumerRecord.value());
            }
        }

    }
}

```

拦截器

```java
public class TimestampInterceptor implements ProducerInterceptor<String, String> {

    /**
     * 拦截ProducerRecord 对数据进行处理并返回
     *
     * @param record
     * @return
     */
    @Override
    public ProducerRecord onSend(ProducerRecord<String, String> record) {
        // 由于ProducerRecord没有set方法，只能创建新对象
        ProducerRecord<String, String> producerRecord = new ProducerRecord<>(
                record.topic(),
                record.partition(),
                record.key(),
                System.currentTimeMillis() + ": " + record.value()
        );
        return producerRecord;
    }

    /**
     * 接收消息发送到服务器后的发送结果信息，在close()方法调用时调用
     *
     * @param metadata
     * @param exception
     */
    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {

    }

    /**
     * 拦截器关闭
     */
    @Override
    public void close() {

    }

    /**
     * 增加和获取配置
     *
     * @param configs
     */
    @Override
    public void configure(Map<String, ?> configs) {

    }
}
```

```java
public class CountInterceptor implements ProducerInterceptor<String, String> {

    private static int success = 0;
    private static int fail = 0;

    @Override
    public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
        // 这里要将null 修改为record
        return record;
    }

    @Override
    public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
        if (metadata != null && exception == null) {
            success++;
        } else {
            fail++;
        }

    }

    @Override
    public void close() {
        System.out.println("sucess: " + success);
        System.out.println("fail: " + fail);
    }

    @Override
    public void configure(Map<String, ?> configs) {

    }
}

```



> 分区器:根据键值确定消息应该处于哪个分区中，默认情况下使用轮询分区，可以自行实现分区器接口自定义分区逻辑
> 序列化器:键序列化器和值序列化器，将键和值都转为二进制流 还有反序列化器 将二进制流转为指定类型数据
> 拦截器:两个方法 doSend()方法会在序列化之前完成 ，onAcknowledgement()方法在消息确认或失败时回调。 可以添加多个拦截器按顺序执行。
>
> 　调用顺序: 拦截器doSend() -> 序列化器 -> 分区器
