## 产品概述

Agora 即时通讯 IM（环信）提供高可靠、低时延、高并发的全球化通信云服务，帮助你快速构建端到端的通信场景。本产品所有文档均简称为即时通讯。

## 功能列表

即时通讯提供以下功能：

### 全消息类型

提供一对一单聊和多人群聊功能，支持文本、表情、图片、语音、视频、地理位置、文件，以及弹幕、红包和礼物等自定义消息类型。除此之外，还提供离线消息、漫游消息等功能。

### 群组管理

提供全面的群组管理能，如群公告发布、群角色设置、群成员管理、群附件发送等。

### 用户管理

提供用户信息存储、用户身份管理等用户体系管理能力，如自定义头像、昵称，以及好友关系、黑名单管理等。

### 离线消息推送

支持集成第三方厂商推送服务。为开发者提供低延时、高送达、高并发、不侵犯用户个人数据的离线推送服务。

### 多端消息同步

支持一个账号同时登录多台设备，实现终端用户的消息通过服务器保存和同步，保证各端均能同步收发消息。

### 消息通知

提供广播消息、自定义消息通知等能力。

### 消息撤回

消息发出后可以进行消息撤回，提供 SDK 和 RESTful API 端两种撤回方式。

## 产品优势

Agora 即时通讯主要具备以下优势：

### 全球部署

Agora 在全球设有中国、新加坡、美国和德国四大数据中心，以及超过 200 个边缘加速节点，网络服务覆盖全球 200 多个国家和地区。

### 超低延时

全球范围内的平均延时小于 200 毫秒，单个区域内的平均延时小于 100 毫秒。

### 高并发

支持同时在线人数无上限，在聊天室内可达到亿级消息并发。

### 高可靠性

- 即时通讯支持同城三数据中心的部署架构，提供 SLA 质量保证，登录成功率 > 99%，全年可用时间高达 99.99%。
- 优异的弱网对抗能力，保证在 70% 丢包情况下，消息到达率仍然为 100%。

## 平台支持

即时通讯支持以下平台，且平台间能够互通：

- Android 4.4+
- iOS 9.0+
- Web（Internet Explorer 9+、FireFox 10+、Chrome 54+、Safari 6+、IE 9+、Edge 12+、QQ 浏览器 8.0+、360 浏览器 10.0+、微信 H5、App 内嵌 H5、Opera 58+）

## 即时通讯与云信令的区别

|          | Agora 即时通讯 IM（环信）   | Agora 云信令（Real-time Messaging）                          |
| :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 使用场景 | 应用于聊天场景，例如：应用内聊天，如单聊、群聊、应用内消息通知等直播聊天室，聊天消息、弹幕、成员管理等 | 应用于信令控制的场景，例如：音视频场景中的实时信令控制其他云信令场景，如 IoT 控制与事件同步、多人游戏指令、服务端消息中间件、AR/VR 控制信令等 |
| 功能     | 单聊、群聊、聊天室用户体系和用户资料丰富的消息类型离线推送多设备同步 | 点对点消息与频道消息离线消息历史消息用户属性与频道属性管理频道人数与成员列表管理查询或订阅用户在线状态呼叫邀请 |