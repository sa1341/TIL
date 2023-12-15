---
author: 임준영
publish_date: 2023-10-01
tags:
  - kafka
---

## 토픽 생성

```sh
 kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic stream-topic
```



## 토픽 상세보기

```sh
kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic stream-topic
```


## 토픽에  Producing

```sh
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic stream-topic
```


## 토픽에 Consuming

```sh
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic stream-topic --from-beginning
```


## 토픽 삭제

```sh
kafka-topics.sh --delete -bootstrap-server localhost:9092 --topic stock-ticker-table
```