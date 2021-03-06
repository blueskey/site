---
layout: post
title:  "HTTP 缓存"
date:   2015-05-21 09:55:59
categories: http
---
项目主页：https://github.com/alibaba/RocketMQ



Administrator@PC-20150729ADE7  /cygdrive/g/work/dev/rocketMQ/alibaba-rocketmq-3.2.6/bin

$ ls

README.md mqadmin.xml mqbroker.numanode0 mqbroker.numanode3 mqfiltersrv.exe mqnamesrv.exe os.sh runserver.sh

mqadmin mqbroker mqbroker.numanode1 mqbroker.xml mqfiltersrv.xml mqnamesrv.xml play.sh startfsrv.sh

mqadmin.exe mqbroker.exe mqbroker.numanode2 mqfiltersrv mqnamesrv mqshutdown runbroker.sh tools.sh

Administrator@PC-20150729ADE7  /cygdrive/g/work/dev/rocketMQ/alibaba-rocketmq-3.2.6/bin

$ nohup sh mqbroker &

[1] 9068

Administrator@PC-20150729ADE7  /cygdrive/g/work/dev/rocketMQ/alibaba-rocketmq-3.2.6/bin

$ nohup: ignoring input and appending output to ‘nohup.out’

$ nohup sh mqnamesrv &

[2] 7944

[1] Exit 127 nohup sh mqbroker

因为此处是在windows环境使用cygwin工具（不是真的linux环境），所以会有问题
windows下：


部署NameServer：

G:\work\dev\rocketMQ\alibaba-rocketmq-3.2.6\bin>mqnamesrv.exe


部署Broker：消息中转角色，负责存储消息，转发消息 ，可支持主－从配置。

G:\work\dev\rocketMQ\alibaba-rocketmq-3.2.6\bin>mqbroker.exe -n localhost:9876


注：不能直接双击mqbroker.exe，会报错，如下：

如果报com.alibaba.rocketmq.client.exception.MQClientException: No route info of this topic, PushTopic
See https://github.com/alibaba/RocketMQ/issues/264 for further details.
at com.alibaba.rocketmq.client.impl.producer.DefaultMQProducerImpl.sendDefaultImpl(DefaultMQProducerImpl.java:570)
at com.alibaba.rocketmq.client.impl.producer.DefaultMQProducerImpl.send(DefaultMQProducerImpl.java:972)
at com.alibaba.rocketmq.client.producer.DefaultMQProducer.send(DefaultMQProducer.java:109)
at com.ruishenh.rocketmq.example.Producer.main(Producer.java:43)

这是因为启动broker需要 mqbroker.exe -n localhost:9876，而不是双击 mqbroker.exe

主从配置：

Master与Slave通过指定相同的brokerName参数来配对，Master的BrokerId必须是0，Slave的BrokerId必须是大于0的数

主（主要看配置文件设置brokerId=0）：mqbroker.exe -n localhost:9876 -c ..\conf\2m-2s-async\broker-a.properties 

brokerClusterName=DefaultCluster
brokerName=broker-a
brokerId=0
deleteWhen=04
fileReservedTime=48
brokerRole=ASYNC_MASTER
flushDiskType=ASYNC_FLUSH

从：mqbroker.exe -n localhost:9876 -c ..\conf\2m-2s-async\broker-a-s.properties   

broker-a-s.properties:
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerId=1
deleteWhen=04
fileReservedTime=48
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH

看conf目录下，几种配置方式 ：



package rocketmq;

import com.alibaba.rocketmq.client.consumer.DefaultMQPushConsumer;
import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import com.alibaba.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import com.alibaba.rocketmq.common.consumer.ConsumeFromWhere;
import com.alibaba.rocketmq.common.message.Message;
import com.alibaba.rocketmq.common.message.MessageExt;

import java.util.List;

/**
 * Created by Administrator on 2016/3/1 0001.
 */
public class Consumer {
    public static void main(String[] args){
        DefaultMQPushConsumer consumer =
                new DefaultMQPushConsumer("PushConsumer");
        consumer.setNamesrvAddr("localhost:9876");
        try {
            //订阅PushTopic下Tag为push的消息
            consumer.subscribe("PushTopic", "push");
            //程序第一次启动从消息队列头取数据
            consumer.setConsumeFromWhere(
                    ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
            consumer.registerMessageListener(
                    new MessageListenerConcurrently() {
                        public ConsumeConcurrentlyStatus consumeMessage(
                                List<MessageExt> list,
                                ConsumeConcurrentlyContext Context) {
                            Message msg = list.get(0);
                            System.out.println(msg.toString());
//                            System.out.println(new String(msg.getBody()));
                            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
                        }
                    }
            );
            consumer.start();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


package rocketmq;

import com.alibaba.rocketmq.client.producer.DefaultMQProducer;
import com.alibaba.rocketmq.client.producer.SendResult;
import com.alibaba.rocketmq.common.message.Message;

/**
 * Created by Administrator on 2016/3/1 0001.
 */
public class Producer {

    public static void main(String[] args){
        DefaultMQProducer producer = new DefaultMQProducer("Producer");
        producer.setNamesrvAddr("localhost:9876");
        try {
            producer.start();

            Message msg = new Message("PushTopic",
                    "push",
                    "1",
                    "Just for test.".getBytes());

            SendResult result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());

            msg = new Message("PushTopic",
                    "push",
                    "2",
                    "Just for test push2.".getBytes());

            result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());

            msg = new Message("PullTopic",
                    "pull",
                    "1",
                    "Just for test.".getBytes());

            result = producer.send(msg);
            System.out.println("id:" + result.getMsgId() +
                    " result:" + result.getSendStatus());
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            producer.shutdown();
        }
    }
}


先启动Consumer ，然后启动 Producer ，查看Consumer输出：

MessageExt [queueId=0, storeSize=142, queueOffset=14, sysFlag=0, bornTimestamp=1456824370936, bornHost=/172.29.7.77:60084, storeTimestamp=1456824370949, storeHost=/172.29.7.77:10911, msgId=AC1D074D00002A9F000000000000175E, commitLogOffset=5982, bodyCRC=753746584, reconsumeTimes=0, preparedTransactionOffset=0, toString()=Message [topic=PushTopic, flag=0, properties={MIN_OFFSET=0, MAX_OFFSET=15, KEYS=1, WAIT=true, TAGS=push1}, body=14]]
B

B
B
B
B
B
B
B
B
B
B
B

