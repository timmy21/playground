## 1. 服务端

#### 增加JAAS文件：config/sasl_scram/kafka_server_jaas.conf
```
KafkaServer {
org.apache.kafka.common.security.scram.ScramLoginModule required
username="admin"
password="admin-secret";
};
```

#### 配置config/sasl_scram/server.properties
```
listeners=SASL_PLAINTEXT://:9092
advertised.listeners=SASL_PLAINTEXT://localhost:9092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=SCRAM-SHA-256
sasl.enabled.mechanisms=SCRAM-SHA-256
```

#### 启动zookeeper
```
bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

#### 创建用户(alice)
```
bin/kafka-configs.sh --zookeeper localhost:2181 --alter --add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret],SCRAM-SHA-512=[password=alice-secret]' --entity-type users --entity-name alice
```

#### 创建用户(admin)
```
bin/kafka-configs.sh --zookeeper localhost:2181 --alter --add-config 'SCRAM-SHA-256=[password=admin-secret],SCRAM-SHA-512=[password=admin-secret]' --entity-type users --entity-name admin
```

#### 启动kafka
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_scram/kafka_server_jaas.conf bin/kafka-server-start.sh -daemon ./config/sasl_scram/server.properties
```

## 2. 客户端

#### 增加JAAS文件：config/sasl_scram/kafka_client_jaas.conf
```
KafkaClient{
org.apache.kafka.common.security.scram.ScramLoginModule required
username="alice"
password="alice-secret";
};
```

#### 增加命名配置文件：config/sasl_scram/command.properties
```
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-256
```

#### 列举主题
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_scram/kafka_client_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_scram/command.properties \
    --list
```

#### 查看主题详情
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_scram/kafka_client_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic test
```
