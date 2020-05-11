---
title: kafka简单入门(概念)
date: 2018-11-05 22:07:46
keywords:
    - kafka
tags:
    - kafka
categories:
    - 消息队列
---

# kafka简介

[Kafka](https://kafka.apache.org)是目前主流的流处理平台，同时作为消息队列家族的一员，其高吞吐性作为很多场景下的主流选择。

<!-- more -->

# 什么是Kafka

- LinkedIn开发
- 2011年初开源，加入Apache基金会
- 2012年从Apache Incubator毕业
- Apache顶级开源

Apache Kafka是一个分布式流媒体平台。这到底是什么意思呢？

## 流媒体平台有三个关键功能：

- 发布和订阅记录流，类似于消息队列或者企业消息传递系统。
- 以容错的持久方式存储记录流。
- 记录发生时处理流。

## Kafka通常用于两大类应用：

- 构建可在系统或应用程序之间可靠获取数据的实时流数据管道
- 构建转换或响应数据流的实时流应用程序


# Kafka概念

## Producer

消息和数据的生产者，向Kafka的一个topic发布消息的进程/代码/服务。

## Consumer

消息和数据的消费者，订阅数据（topic）并且处理其发布的消息的进程/代码/服务。

## Consumer Group

对于同一个topic，会广播给不同的group，一个group中，只有 ** 一个consumer ** 可以消费该消息。

## Broker

物理概念，Kafka集群中每个kafka节点。

## Topic

逻辑概念，Kafka消息的类别，对数据进行区分、隔离。

## Partition

物理概念，Kafka下数据存储的基本单元。一个Topic数据，会被分散存储到多个Partition，每个Partition是 ** 有序的 ** 。

- 每一个Topic被切分为多个Partitions
- 消费者数目少于或者等于Partition的数目
- Broker Group中的每一个Broker保存Topic的一个或多个Partitions
- Consumer Group的仅有一个Consumer读取Topic的一个或者多个Partitions，并且是唯一的Consumer

## Replication

一个partition的多个副本。同一个Partition可能有多个Replica，多个Replica之间数据是一样的，相当于备份一样。

- 当集群中有Broker挂掉的情况，系统可以主动的使Replicas提供服务
- 系统默认设置每一个Topic的Replication系数为1，可以在创建Topic单独设置

### 特点

- 基本单位是Topic的Partition
- 所有的读和写都从Leader进，Followers只是作为备份
- Follower必须能够及时复制Leader的数据
- 增加容错性和可扩展性。

## Replication Leader

一个Partition的多个Replica上，需要一个Leader负责该Partition上的Producer和Consumer交互。且有且只有一个。

## ReplicaManager

负责管理当前Broker所有分区的副本的信息，处理KafkaController发起的一些请求，副本状态的切换、添加/读取消息等。








