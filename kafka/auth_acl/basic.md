## 1. 服务端

#### 安装java
```
yum install -y java-1.8.0-openjdk-devel.x86_64
```

#### 下载
```
wget https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.4.1/kafka_2.12-2.4.1.tgz
```

#### 解压到/opt
```
tar -xzf kafka_2.12-2.4.1.tgz -C /opt
```

#### 修改配置，编辑config/server.properties
```
# 监听所有接口和ip：
listeners=PLAINTEXT://:9092
# 修改广播ip：
advertised.listeners=PLAINTEXT://localhost:9092
```

#### 启动zookeeper
```
bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

#### 启动kafka
```
bin/kafka-server-start.sh -daemon ./config/server.properties
```

## 2. 客户端

#### 列举主题
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

#### 创建主题
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic test --partitions 3 --replication-factor 1
```

#### 查看主题详情
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic test
```

#### 删除主题
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic test
```

#### 查看消费进度
```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group test
```

#### 重新设置消费者位移
```
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group test --topic test --reset-offsets --to-earliest --execute
```
