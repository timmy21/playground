本文介绍SASL/PLAIN模式下，如何配置ACL的问题

## 服务端
#### 设置config/sasl_plain/server.properties
```
authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer
super.users=User:admin
# allow.everyone.if.no.acl.found=false
```

#### 启动zookeeper
```
bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

#### 启动kafka
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_server_jaas.conf bin/kafka-server-start.sh -daemon ./config/sasl_plain/server.properties
```

## 客户端

#### 增加admin的JAAS文件：config/sasl_plain/kafka_client_admin_jaas.conf
```
KafkaClient{
org.apache.kafka.common.security.plain.PlainLoginModule required
username="admin"
password="admin-secret";
};
```

#### 创建主题
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_admin_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --create \
    --partitions 3 \
    --replication-factor 1 \
    --topic test
```

#### 列举主题
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_admin_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --list
```

#### 查看主题详情
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_admin_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --describe \
    --topic test
```

#### 删除主题
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_admin_jaas.conf \
    bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --delete \
    --topic test
```

#### 设置生产者权限
```
bin/kafka-acls.sh \
    --authorizer-properties zookeeper.connect=localhost:2181 \
    --add \
    --allow-principal User:alice \
    --producer \
    --topic 'test'
```

#### 设置消费者权限
```
bin/kafka-acls.sh \
    --authorizer-properties zookeeper.connect=localhost:2181 \
    --add \
    --allow-principal User:alice \
    --consumer \
    --topic 'test' --group 'test'
```

#### 查询权限列表
```
bin/kafka-acls.sh \
    --authorizer-properties zookeeper.connect=localhost:2181 \
    --list \
    --topic 'test'
```

### 测试消费
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_jaas.conf \
    bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
    --consumer.config config/sasl_palin/command.properties \
    --from-beginning \
    --topic test --group test
```

#### 查看消费进度
```
KAFKA_OPTS=-Djava.security.auth.login.config=/opt/kafka_2.12-2.4.1/config/sasl_plain/kafka_client_jaas.conf \
    bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
    --command-config config/sasl_plain/command.properties \
    --describe \
    --group test
```
