# 常用命令操作手册

---

### 一、Linux

- 同步数据到其他服务器

  ```bash
  scp ./kafka_2.12-3.0.0.tgz root@tech2.xiu.com:/opt/hadoop/kafka
  ```

- 

### 二、Kafka

#### 	1、启动

- `后台常驻方式，带上参数 -daemon，如：`

  ```
  kafka-server-start.sh -daemon /opt/hadoop/kafka/cluster/kafka-3.0/config/server.properties
  ```

- `指定 JMX port 端口启动，指定 jmx，可以方便监控 Kafka 集群` 

  ```bash
  JMX_PORT=9991 kafka-server-start.sh -daemon /usr/local/kafka/config/server.properties
  ```

#### 	2、停止

```bash
kafka-server-stop.sh
```

#### 	3、Topic

##### 			3-1.创建 Topic

​			`参数 --topic 指定 Topic 名，--partitions 指定分区数，--replication-factor 指定备份数`

```bash
kafka-topics.sh --create --bootstrap-server tech1.xiu.com:9092 --replication-factor 1 --partitions 1 --topic test1
```

​			`注意，如果配置文件 server.properties 指定了 Kafka 在 zookeeper 上的目录，则参数也要指定，否则会报无可用的 brokers（下面部分命令也有同样的情况），如：`

```bash
/usr/local/kafka/bin/kafka-topics.sh --create --zookeeper localhost:2181/kafka --replication-factor 1 --partitions 1 --topic test
```

##### 			3-2.列出所有 Topic

```bash
kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092

kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092,tech2.xiu.com:9092,tech3.xiu.com:9092
```

##### 			3-3.生产数据：

```bash
kafka-console-producer.sh --broker-list tech1.xiu.com:9092 --topic test
```

##### 			3-4.查看数据：

```bash
kafka-console-consumer.sh --bootstrap-server tech2.xiu.com:9092  --topic test --from-beginning
```

##### 3-5.列出所有topic

```bash
kafka-topics.sh --list --bootstrap-server tech1.xiu.com:9092,tech2.xiu.com:9092,tech3.xiu.com:9092 
```

##### 3-6.群起群关脚本

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









​	