---
title: 按频道人数计时和按流计时有什么区别？
platform: ["All Platforms"]
updatedAt: 2021-02-03 03:42:03
Products: ["Voice","Video","Interactive Broadcast","Recording","cloud-recording"]
---
RTC 领域，有两种不同的计时方式：按频道人数计时和按流计时。目前声网使用的是按**频道人数**计时的方式。

## 按频道人数计时

计算方式：如果频道中有 n 人参与通话 m 分钟，则通话总分钟数 = n * m。

例如：

- 2 个人视频通话 10 分钟，则通话总时长为 2 * 10 = 20 分钟
- 5 个人视频通话 10 分钟，则通话总时长为 5 * 10 = 50 分钟
- 10 个人视频通话 10 分钟，则通话总时长为 10 * 10 = 100 分钟

这种计时方式下，如果一个用户同时订阅了多路音视频流，其音视频分钟数不会被叠加。

## 按流计时

计算方式：如果频道中有 n 人参与通话 m 分钟，且每个用户都订阅了其他所有用户的流，则通话总分钟数 = n * (n-1) * m。

例如：

- 2 个人视频通话 10 分钟，则通话总时长为 2 * (2-1) * 10 = 20 分钟
- 5 个人视频通话 10 分钟，则通话总时长为 5 * (5-1) * 10 = 200 分钟
- 10 个人视频通话10 分钟，则通话总时长为 10 * (10-1) * 10 = 900 分钟

这种计时方式下，如果一个用户同时订阅了多路音视频流，其接收的每路音视频流都会纳入进来进行叠加计时。

## 区别

两种计时方式对比如下：

| 场景 | 按频道人数计时 | 按流计时 |
| ------------ | ------------- | --------------- |
| 2 个人视频通话 10 分钟 |	20 分钟 | 20 分钟 |
| 5 个人视频通话 10 分钟 |	50 分钟 |	200 分钟 |
| 10 个人视频通话 10 分钟	| 100 分钟	| 900 分钟 |

房间人数越多，按频道人数计时和按流计时之间的分钟数差异越大。

## 声网计时方式

目前声网使用的是**按频道人数计时**的方式。相比而言，这种方式更简单、也更直观。

## 相关链接
[实时音视频计费说明](https://docs.agora.io/cn/Interactive%20Broadcast/billing_rtc?platform=Android)