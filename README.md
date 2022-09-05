# rsqldb

rsqldb 为 RocketMQ Streams 的开发提供了基于 SQL 的开发体验， 让基于消息队列的流式开发更加容易；

## Features

* 采用标准的流式SQL规范，可以与其他的流计算框架如Flink完美兼容；
* 兼容Flink自带的```udf```、```udaf```和```udtf```，除此之外，用户还可以通过实现相关接口来轻松扩展函数；


如果您希望更详细的了解rsqldb的相关内容， 请点击[这里](docs/SUMMARY.md)


## Quickstart
### 本地安装docker，[安装链接](https://docs.docker.com/desktop/install/mac-install/)
安装后启动docker
使用docker -v 能查看docker版本即是启动成功。

### 下载rsqldb工程
```shell
git clone https://github.com/alibaba/rsqldb.git
```

### 进入工程目录并执行启动docker
```shell
cd rsqldb
docker-compose -f docker-compose.yml up
```
此为前台启动，也可使用 docker-compose -f docker-compose.yml up -d 方式后台启动。
### 进入rsqldb-client容器
另开shell窗口，进入rsqldb-client容器，进入方式：
```shell
#查看容器
docker ps
#进入rsqldb-client容器
docker exec -it ${rsqldb-client-containerId} bash
```
### 提交任务
```shell
sh clientExector.sh submitTask rocketmq.sql
```
rocketmq.sql任务从RocketMQ中rsqldb-source topic中读取数据，过滤出数据中首字段为1的数据，然后将结果写回到RocketMQ
中的rsqldb-sink topic中。
### 开始任务
```shell
sh clientExector.sh startTask
```

### 向RocketMQ中写入数据
```shell
java -cp RocketmqTest-1.0-SNAPSHOT.jar  com.test.rocketmqtest.producer.Producer
```
执行上述语句，向RocketMQ的rsqldb-source topic中写入RocketmqTest-1.0-SNAPSHOT.jar包中默认数据，即向rocketmq发送的数据为：
```xml
1,2,3,4
2,2,3,4
3,2,3,4
4,2,3,4
```
（也可以向rocketmq发送json数据，显式指定数据数据哪个字段，见rsqldb-disk/client中其他例子）
根据执行的任务rocketmq.sql，期望能将field_1=1的数据过滤出来；

另外，也可以使用RocketmqTest-1.0-SNAPSHOT.jar向任意topic发送任意数据，使用方式是：
``shell
java -cp RocketmqTest-1.0-SNAPSHOT.jar  com.test.rocketmqtest.producer.Producer ${topic} ${groupId} ${数据文件权路径}
``
### 查看结果输出
```shell
java -cp RocketmqTest-1.0-SNAPSHOT.jar  com.test.rocketmqtest.consumer.Consumer
```
从RocketMQ的rsqldb-sink topic中读出结果数据，每执行一次producer会有一行输出(第一次需要等待1min轮询提交的任务)：
```xml
Receive New Messages: body[{"field_3":"3","field_4":"4","field_1":"1","field_2":"2"}]
```
Consumer类允许带两个参数：topic、groupId，可以指定topic消费RocketMQ数据。


## 其他启动方式
### 本地jar包启动
  [本地jar包启动](docs/other_quick_start/本地jar包启动.md)
### 本地工程启动
  [本地工程启动](docs/other_quick_start/本地工程启动.md)


