## 1. 服务端

#### 增加JAAS文件：config/sasl_plain/kafka_server_jaas.conf
```
KafkaServer {
org.apache.kafka.common.security.plain.PlainLoginModule required
username="admin"
password="admin-secret"
user_admin="admin-secret"
user_alice="alice-secret";
};
```

#### 配置config/sasl_plain/server.properties
```
listeners=SASL_PLAINTEXT://:9092
advertised.listeners=SASL_PLAINTEXT://localhost:9092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN
```

#### 启动zookeeper
```
bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

#### 启动kafka
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_server_jaas.conf bin/kafka-server-start.sh -daemon ./config/sasl_plain/server.properties
```

## 2. 客户端

#### 增加JAAS文件：config/sasl_plain/kafka_client_jaas.conf
```
KafkaClient{
org.apache.kafka.common.security.plain.PlainLoginModule required
username="alice"
password="alice-secret";
};
```

#### 增加命名配置文件：config/sasl_plain/command.properties
```
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```

#### 列举主题
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --list
```

#### 查看主题详情
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic test
```
