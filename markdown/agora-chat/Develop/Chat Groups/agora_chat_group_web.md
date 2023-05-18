群组是支持多人沟通的即时通讯系统。本文介绍如何使用即时通讯 IM SDK 在实时互动 app 中创建和管理群组，并实现群组相关功能。

## 技术原理

即时通讯 IM SDK 支持你通过调用 API 在项目中实现以下群组管理功能：

- 创建、解散群组
- 获取群组详情信息
- 获取群成员列表
- 获取群组列表
- 监听群组事件

## 前提条件

开始前，请确保满足以下条件：

- 完成 SDK 初始化，详见 [Web 快速开始](./agora_chat_get_started_web)。
- 了解即时通讯 IM 的[使用限制](./agora_chat_limitation)。
- 了解各套餐包的群组和群成员的数量限制，详见[各套餐包功能使用限制](./agora_chat_pricing#各套餐包功能使用限制)。

## 实现方法

### 创建群组

1、调用 `createGroup` 方法新建群组，设置群组参数。

群组分为私有群和公开群。私有群无法搜索到，公开群可通过群 ID 搜索到。

创建群组时，需设置以下参数：

| 参数 | 类型        | 描述                 |
| :--------- | :----------------------- | :----------------------------- |
| `groupname`  | String             | 群组名称。              |
| `desc`  | String | 群组描述。           |
| `members`    | Array         | 群成员的用户 ID 组成的数组。 |
| `public`  | Boolean             | 是否为公开群：<ul><li>`true`：是；</li><li>`false`：否。该群组为私有群。</li></ul> |
| `approval`  | Boolean | 入群申请是否需群主或管理员审批：<ul><li>`true`：需要；</li><li>`false`：不需要。</li></ul><br/>由于私有群不支持用户申请入群，只能通过邀请方式进群，因此该参数仅对公开群有效，即 `public` 设置为 `true` 时，对私有群无效。  |
| `allowinvites`    | Boolean         | 是否允许普通群成员邀请人入群：<ul><li>`true`：允许；</li><li>`false`：不允许。只有群主和管理员才可以向群组添加用户。</li></ul><br/>该参数仅对私有群有效，即 `public` 设置为 `false` 时， 因为公开群（`public` 为 `true`）仅支持群主和群管理员邀请人入群，不支持普通群成员邀请人入群。 |
| `inviteNeedConfirm`  | Boolean             | 邀请加群时是否需要受邀用户确认：<ul><li>`true`：受邀用户需同意才会加入群组；</li><li>`false`：受邀用户直接加入群组，无需确认。</li></ul> |
| `maxusers`  | Number             | 群组最大成员数。          |
| `ext`   | String | 群组详情扩展信息。                   |

创建群组的示例代码如下：

```javascript
let option = {
  data: {
    groupname: "groupName",
    desc: "A description of a group",
    members: ["user1", "user2"],
    public: true,
    approval: true,
    allowinvites: true,
    inviteNeedConfirm: true,
    maxusers: 500,
    ext: "group detail extensions",
  },
};
conn.createGroup(option).then((res) => console.log(res));
```

2、邀请用户入群。

公开群只支持群主和管理员邀请用户入群。对于私有群，除了群主和群管理员，群成员是否也能邀请其他用户进群取决于 `allowinvites` 选项的设置：

- `true`：允许；
- `false`：不允许。只有群主和管理员才可以向群组添加用户。

邀请用户加群流程如下：

1. 群成员调用 `inviteUsersToGroup` 方法邀请用户入群。

```javascript
conn.inviteUsersToGroup({ groupId: "groupId", users: ["user1", "user2"] });
```

2. 受邀用户会收到 `inviteToJoin` 事件，自动进群或确认是否加入群组。

   入群邀请是否需受邀用户确认取决于群组选项 `inviteNeedConfirm` 的设置：

   - `inviteNeedConfirm` 设置为 `false` 时，受邀用户直接进群，无需确认，群组所有成员会收到 `memberPresence` 事件。

   - `inviteNeedConfirm` 设置为 `true` 时，受邀用户需确认是否加入群组。

     - 受邀用户同意加入群组，需要调用 `acceptGroupJoinRequest` 方法。用户加入成功后，邀请人会收到 `acceptInvite` 事件，群组所有成员会收到 `memberPresence` 事件。

     ```javascript
     conn.acceptGroupInvite({ invitee: "myUserId", groupId: "groupId" });
     ```
     - 受邀用户拒绝入群，需要调用 `rejectGroupJoinRequest` 方法。邀请人会收到 `rejectInvite` 事件。

     ```javascript
     conn.rejectGroupInvite({ invitee: "myUserId", groupId: "groupId" });
     ```

3、用户加入群组后，可以收发群消息。

### 解散群组

仅群主可以调用 `destroyGroup` 方法解散群组。群组解散时，其他群组成员收到 `destroy` 事件并被踢出群组。

示例代码如下：

```javascript
let option = {
  groupId: "groupId",
};
conn.destroyGroup(option).then((res) => console.log(res));
```

### 获取群组详情信息

所有群成员均可调用 `getGroupInfo` 方法根据群组 ID 获取群组详情，包括群组 ID、群组名称、群组描述、群组基本属性、群主、群组管理员列表、是否已屏蔽群组消息以及群组是否禁用。默认不包含群成员列表。

示例代码如下：

```javascript
let option = {
    // 群组 ID 或群组 ID 数组。
    groupId: 'groupId'
};
conn.getGroupInfo(option).then((res) => {
    console.log(res)
})
```

### 获取群成员列表

所有群成员均可调用 `listGroupMembers` 方法以分页方式获取群成员列表。

示例代码如下：

```javascript
let pageNum = 1,
  pageSize = 100;
let option = {
  pageNum: pageNum,
  pageSize: pageSize,
  groupId: "groupId",
};
conn.listGroupMembers(option).then((res) => console.log(res));
```

### 获取群组列表

- 用户可以调用 `getJoinedGroups` 方法获取当前用户加入和创建的群组列表，示例代码如下：

```javascript
conn.getJoinedGroups({
                 pageNum: 1,
                 pageSize: 500,
                 needAffiliations: true,
                 needRole: true
  })
```

- 用户还可以分页获取公开群列表：

```javascript
let option = {
    limit: 20,
    cursor: cursor,
};
conn.getPublicGroups(option).then(res => console.log(res))
```

### 监听群组事件

SDK 提供 `addEventHandler` 方法用于注册群组事件监听器。开发者可以通过设置此监听，获取群组中的事件。

示例代码如下：

```javascript
// 创建一个群组事件监听器
conn.addEventHandler("eventName", {
  onGroupEvent: function (msg) {
    switch (msg.operation) {
      // 有新群组创建。群组创建后，群主的其他设备会收到该回调。
      case "create":
        break; 
      // 关闭群组一键禁言。群组所有成员（除操作者外）会收到该回调。
      case "unmuteAllMembers":
        break;
      // 开启群组一键禁言。群组所有成员（除操作者外）会收到该回调。
      case "muteAllMembers":
        break;
      // 有成员从群白名单中移出。被移出的成员及群主和群管理员（除操作者外）会收到该回调。
      case "removeAllowlistMember":
        break;
      // 有成员添加至群白名单。被添加的成员及群主和群管理员（除操作者外）会收到该回调。
      case "addUserToAllowlist":
        break;
      // 删除群共享文件。群组所有成员会收到该回调。
      case "deleteFile":
        break;
      // 上传群共享文件。群组所有成员会收到该回调。
      case "uploadFile":
        break;
      // 删除群公告。群组所有成员会收到该回调。
      case "deleteAnnouncement":
        break;
      // 更新群公告。群组所有成员会收到该回调。
      case "updateAnnouncement":
        break;
      // 更新群组信息，如群组名称和群组描述。群组所有成员会收到该回调。
      case "updateInfo":
        break;  
      // 有成员被移出禁言列表。被解除禁言的成员及群主和群管理员（除操作者外）会收到该回调。
      case "unmuteMember":
        break;
      // 有群组成员被加入禁言列表。被禁言的成员及群主和群管理员（除操作者外）会收到该回调。
      case "muteMember":
        break;
      // 有管理员被移出管理员列表。群主、被移除的管理员和其他管理员会收到该回调。
      case "removeAdmin":
        break;
      // 设置管理员。群主、新管理员和其他管理员会收到该回调。
      case "setAdmin":
        break;
      // 转让群组。原群主和新群主会收到该回调。
      case "changeOwner":
        break;
      // 群主和管理员拉用户进群时，无需用户确认时会触发该回调。被拉进群的用户会收到该回调。
      case "directJoined":
        break;
      // 群成员主动退出群组。除了退群的成员，其他群成员会收到该回调。
      case "memberAbsence":
        break;
      // 有用户加入群组。除了新成员，其他群成员会收到该回调。
      case "memberPresence":
        break;
      // 用户被移出群组。被踢出群组的成员会收到该回调。
      case "removeMember":
        break;
      // 当前用户的入群邀请被拒绝。邀请人会收到该回调。
      case "rejectInvite":
        break;
      // 当前用户的入群邀请被接受。邀请人会收到该回调。
      case "acceptInvite":
        break;
      // 当前用户收到了入群邀请。受邀用户会收到该回调。
      case "inviteToJoin":
        break;
      // 当前用户的入群申请被拒绝。申请人会收到该回调。
      case "joinPublicGroupDeclined":
        break;
      // 当前用户的入群申请被接受。申请人会收到该回调。
      case "acceptRequest":
        break;
      // 当前用户发送入群申请。群主和群管理员会收到该回调。
      case "requestToJoin":
        break;
      // 群组被解散。群主解散群组时，所有群成员均会收到该回调。
      case "destroy":
        break;
      default:
        break;
    }
  },
});

```