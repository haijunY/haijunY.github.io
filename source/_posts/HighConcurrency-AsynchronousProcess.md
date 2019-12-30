---
title: 异步处理
date: 2018-07-25 15:07:00
categories: 高并发
tags: 
---
异步处理的实现方式有几种：
##  定时任务
适用于处理任务时间很长，并且即时要求不高的情况，例如生成pdf合同文件，生成报表，定期同步系统之间的数据
详情参考[定时任务](https://www.haijunyin.com/HighConcurrency-TimingTask.html)
## 多线程处理
适用于处理任务时间短，但并发的量比较大，并且不需要同步返回结果的情况，例如短信发送、消息推送kafka
详情参考[多线程](https://www.haijunyin.com/HighConcurrency-multithreading.html)
## 消息队列kafka
把执行任务加入消息队列，排队处理，适用于并发量非常大的业务，比如火车票抢票、秒杀限量商品
详情参考[消息队列kafka](https://haijunyin.com/HighConcurrency-kafka.html)