本文展示即时通讯 IM 的计费方式。

## 概述

当你使用即时通讯服务时，声网按月计算套餐包费用和套餐超额费用（如有），并[发布账单、进行扣款](https://docs.agora.io/cn/InteractiveBroadcast/faq/billing_account)。如果在当月开通、取消或变更套餐包，则按照当月使用天数占当月天数比例进行收费。如果[账户冻结](https://docs.agora.io/cn/agora-chat/faq/billing_account?platform=AllPlatforms)，声网提供 6 个月的应用数据保存服务，你需要及时解冻账户或在数据被删除前进行导出。

## 费用计算

每月结束时，声网会统计你的[声网账号](https://docs.agora.io/cn/AgoraPlatform/get_appid_token?platform=AllPlatforms#创建声网账号)下即时通讯项目的用量，扣除套餐包内用量额度后，如有额外用量，则将额外用量乘以相应的单价，加上套餐包费用得出本月总费用。计算公式如下：

**费用** = 套餐包费用 + 额外用量 × 套餐包超额单价

### 用量

声网根据账户中全部即时通讯项目的当月最高 DAU（日活跃用户数量）计算用量。

> 如果一个用户调用 `login` 方法成功登录即时通讯服务器，并与之建立长连接，则将该用户视为活跃。

### 套餐包用量额度

套餐包分为免费版、基础版、进阶版和企业版，你可以根据业务需求进行选择，各版本包含的功能与限制，详见[各套餐包功能使用限制](./agora_chat_pricing#各套餐包功能使用限制)。用量额度及费用见下表：

| 套餐包版本 | 套餐包费用（元/月） | 用量额度（DAU/月） | 套餐包超额单价（元/万 DAU） |
| :--------- | :------- | :---------- | :-------- |
| 免费版     | 0                   | 100                | 订阅收费套餐包              |
| 基础版     | 888                 | 10,000             | 850                         |
| 进阶版     | 2,888               | 10,000             | 850                         |
| 企业版     | 4,888               | 10,000             | 850                         |

## 计费示例

某用户购买的即时通讯的套餐包为基础版，并在项目 A 和项目 B 中启用了即时通讯服务，项目 A 的当月最高 DAU 为 82,370，项目 B 的当月最高 DAU 为 17,865，扣除用量额度 10,000 后，当月即时通讯服务费用为 (82,370 + 17,865 - 10,000) * 0.085 + 888 = 8,557.975 元。

## 管理套餐包

### 订阅套餐包

在使用即时通讯服务之前，你需要参考如下步骤订阅即时通讯套餐包：

1. 登录[声网控制台](https://console.agora.io/)，点击左侧菜单栏中的**套餐包** > **即时通讯IM** > **订阅**。
2. 查看各套餐包的[功能详情](#package)，根据业务需要选择套餐包，点击**订阅**，并完成支付。

订阅后，套餐包立即生效。你可以在**套餐包** > **即时通讯 IM** > **管理**页面点击**查看详情**，查看订阅详情。

> 所有版本的即时通讯套餐包都默认开启自动续订，且支持随时取消或更改订阅。

### 取消或更改订阅

订阅即时通讯套餐包后，如果需要取消订阅或者更换套餐包，可参考以下步骤：

#### 取消订阅

1. 在**套餐包** > **即时通讯 IM** > **管理**页面点击**查看详情**，查看订阅详情。
2. 点击**取消订阅**。

#### 更换套餐包

你可以点击其他套餐的订阅按钮升级套餐包，或通过联系 [sales@agora.io](mailto:sales@agora.io) 降级套餐包：

1. 在**套餐包** > **即时通讯 IM** > **订阅**页面，选择想要更换的套餐包，点击**立即订阅**。
2. 仔细阅读弹窗提示内容后，点击**保存**。

> - 取消订阅或者更换套餐包将于次月 1 日生效，不影响当月使用。
> - 当月的订阅费用不会退款，当月产生的超额用量会按照原有套餐包的超额价格收取费用。

<a name="package"></a>

### 各套餐包功能使用限制

各版本的套餐包支持的功能和使用限制见下表：

#### 用户

| 功能描述            | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------- | :----- | :----- | :----- | :----- |
| 用户好友数                | 100    | 250   | 1,000 | 10,000 |
| 注册用户总数        | 无上限    | 无上限 | 无上限 | 无上限 |
| 同时在线用户数      | 50    | 月活（MAU）的 10% | 月活（MAU）的 10% | 自定义（月活的 10%） |
| 用户属性（提供用户头像和昵称等信息的数据存储服务）        | ✓    | ✓ | ✓ | ✓ |

#### 消息

| 功能描述                                                     | 免费版 | 基础版 | 进阶版 | 企业版 |
| :----------------------------------------------------------- | :----- | :----- | :----- | :----- |
| 消息云存储（提供消息存储服务，支持历史消息、漫游消息和离线消息） | 7 天  | 90 天 | 180 天  | 自定义（默认 180 天） |
| 全消息类型（包括文本、表情、语音、视频、图片、位置、透传和自定义等类型的消息） | ✓      | ✓      | ✓      | ✓      |
| 自定义消息（根据业务需求灵活定制消息内容和功能）             | ✓      | ✓      | ✓      | ✓      |
| 透传消息（控制指令）                                     | ✓      | ✓      | ✓      | ✓      |
| 离线消息（支持单聊/群聊离线消息，上线后可拉取离线消息）      | ✓      | ✓      | ✓      | ✓      |

#### 群组

| 功能描述         | 免费版 | 基础版 | 进阶版 | 企业版 |
| :--------------- | :----- | :----- | :----- | :----- |
| 群组总数         | 100    | 10,0000   | 50,000 | 自定义（默认为 100,000） |
| 群成员数         | 100    | 250    | 1,000   | 自定义（默认为 5,000）   |
| 用户可加入的群组数 | 100    | 1,000    | 2,000 | 自定义（默认为 10,000） |

#### 聊天室

| 功能描述                               | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------------------------- | :----- | :----- | :----- | :----- |
| 聊天室数量 | 100      | 10,000    | 50,000 | 自定义（默认为 100,000） |
| 聊天室成员数               | 100      | 2,000      | 10,000      | 自定义（默认为 200,000）     |
| 用户可加入的聊天室数量                 | 100      | 1,000      | 2,000     | 自定义（默认为 10,000）     |
| 实时互动聊天室广播消息                 | ✓      | ✓      | ✓      | ✓      |
| 实时互动聊天室全局禁言                 | ✓      | ✓      | ✓      | ✓      |
| 实时互动聊天室消息优先级                 | ✓      | ✓      | ✓      | ✓      |
| 实时互动聊天室用户白名单               | ✓     | ✓      | ✓      | ✓      |
| 实时互动聊天室历史消息存储             | ✓      | ✓      | ✓      | ✓      |

#### 功能特性

| 功能描述                                                     | 免费版 | 基础版 | 进阶版 | 企业版 |
| :----------------------------------------------------------- | :----- | :----- | :----- | :----- |
| 消息和事件回调（提供全量消息路由和转发，支持消息和多种事件类型） | ✖      | ✖      | ✓      | ✓      |
| 用户状态回调（通过回调方式同步用户离在线状态）               | ✖      | ✖      | ✓      | ✓      |
| 发送前回调（用于对接第三方消息审核服务）                     | ✖      | ✖      | ✓      | ✓      |
| 多端多设备在线（支持不同设备同时在线，模拟消息接收）         | ✓      | ✓      | ✓      | ✓      |
| 消息已读回执（单聊回执均支持，群聊回执进阶版及以上版本支持）       | ✓      | ✓      | ✓      | ✓      |
| 消息撤回（支持客户端/RESTful 消息撤回）                          | ✓      | ✓      | ✓      | ✓      |
| 消息免打扰（在设置的免打扰时间内不收推送消息）                 | ✓      | ✓      | ✓      | ✓      |
| 服务端会话列表（Web 端拉取历史会话）                          | ✓      | ✓      | ✓      | ✓      |
| IM UIkit/Callkit（提供 IM 和 RTC UI 组件）      | ✓      | ✓      | ✓      | ✓      |
| 全平台离线推送（支持苹果、谷歌、华为、小米、OPPO、VIVO、魅族，自定义铃声和扩展） | ✓      | ✓      | ✓      | ✓      |
| 群组文件共享      | ✓      | ✓      | ✓     | ✓      |
| 生成图片消息缩略图      | ✓      | ✓      | ✓      | ✓      |
| 接收离线消息      | ✓      | ✓      | ✓      | ✓      |
| 群组和聊天室通知      | ✓      | ✓      | ✓      | ✓      |
| 消息表情 Reaction      | ✓      | ✓      | ✓      | ✓      |
| 子区（Thread）     | ✓      | ✓      | ✓      | ✓      |
| 用户在线状态（Presence）      | ✓     | ✓      | ✓      | ✓      |
| 翻译      | ✖      | ✖      | ✓      | ✓      |

#### 内容审核

| 功能描述        | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------------ | :----- | :----- | :----- | :----- |
| 消息举报 | ✖      | ✖      | ✓      | ✓      |
| 封禁用户 | ✓      | ✓      | ✓      | ✓      |
| 禁言用户 | ✓      | ✓      | ✓      | ✓      |
| 实时审核 | ✖      | ✖      | ✓      | ✓      |
| 群组和聊天室全员禁言 | ✓      | ✓      | ✓      | ✓      |
| 用户 ID 过滤 | ✖      | ✖      | ✓      | ✓      |
| 文本消息审核 | ✖      | ✖      | ✓      | ✓      |
| 图片消息审核 | ✖      | ✖      | ✓      | ✓      |
| 音频消息审核 | ✖      | ✖      | ✓      | ✓      |
| 视频消息审核 | ✖      | ✖      | ✓      | ✓      |
| 审核标签 | ✖      | ✖      | ✓      | ✓      |
| 上报消息 | ✖      | ✖      | ✓      | ✓      |

#### 水晶球

| 功能描述        | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------------ | :----- | :----- | :----- | :----- |
| 用量监控 | ✓      | ✓      | ✓      | ✓      |
| 质量监控 | ✓      | ✓      | ✓      | ✓      |

#### 安全

| 功能描述        | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------------ | :----- | :----- | :----- | :----- |
| TLS/SSL 加密 | ✓      | ✓      | ✓      | ✓      |
| 文件加密 | ✓      | ✓      | ✓      | ✓      |
| 个人数据删除 API | ✓      | ✓      | ✓      | ✓      |
| IP 白名单 | ✓      | ✓      | ✓      | ✓      |

#### 合规

| 功能描述        | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------------ | :----- | :----- | :----- | :----- |
| ISO27001 | ✓      | ✓      | ✓      | ✓      |
| GDPR | ✓      | ✓      | ✓      | ✓      |
| SOC 2 | ✓      | ✓      | ✓      | ✓      |
| HIPPA | ✓      | ✓      | ✓      | ✓      |

#### 网络

| 功能描述                                                  | 免费版 | 基础版 | 进阶版 | 企业版 |
| :-------------------------------------------------------- | :----- | :----- | :----- | :----- |
| 全球加速网络（SD-GMN，全球 5 大数据中心、200+ 边缘加速节点） | ✓      | ✓      | ✓      | ✓      |

#### RESTful 运营服务

| 功能描述             | 免费版 | 基础版 | 进阶版 | 企业版 |
| :------------------- | :----- | :----- | :----- | :----- |
| RESTful API 调用频率 | 一般 100/秒  | 一般 100/秒  | 一般 100/秒  | 一般 100/秒 |
| 发送系统消息         | ✓      | ✓      | ✓      | ✓      |
| 发送单聊消息         | ✓      | ✓      | ✓      | ✓      |
| 发送群组消息         | ✓      | ✓      | ✓      | ✓      |
| 发送聊天室消息       | ✓      | ✓      | ✓      | ✓      |
| 封禁用户             | ✓      | ✓      | ✓      | ✓      |
| 将用户加入黑名单     | ✓      | ✓      | ✓      | ✓      |
| RESTful API 消息撤回 | ✓      | ✓      | ✓      | ✓      |
| 查询用户在线状态     | ✓      | ✓      | ✓      | ✓      |

关于服务端 RESTful API 调用频率限制：

- 除获取聊天记录文件 RESTful API，所有即时通讯 RESTful API 的调用频率都针对单个 IP 地址；
- 获取聊天记录文件 API 的调用频率上限为针对单个 app 1 次/分钟；
- 具体接口调用频率限制详见[限制条件](./agora_chat_limitation)。

#### SDK 支持

| 平台    | 免费版 | 基础版 | 进阶版 | 企业版 |
| :----------- | :----- | :----- | :----- | :----- |
| Android    | ✓      | ✓      | ✓      | ✓      |
| iOS      | ✓      | ✓      | ✓      | ✓      |
| Web       | ✓      | ✓      | ✓      | ✓      |
| 小程序       | ✓      | ✓      | ✓      | ✓      |