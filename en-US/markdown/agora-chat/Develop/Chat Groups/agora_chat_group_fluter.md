# Manage Chat Groups

Chat groups enable real-time messaging among multiple users.

This page shows how to use the Agora Chat SDK for Flutter to create and manage a chat group in your app.


## Understand the tech

The Agora Chat SDK provides the `ChatGroup`, `ChatGroupManager`, and `ChatGroupEventListener` classes for chat group management, which allows you to implement the following features:

- Create and destroy a chat group
- Join and leave a chat group
- Retrieve the chat group attributes
- Retrieve the chat group member list
- Retrieve the chat group list
- Block and unblock a chat group
- Listen for chat group events


## Prerequisites

Before proceeding, ensure that you meet the following requirements:

- You have initialized the Agora Chat SDK. For details, see [Get Started with Flutter](./agora_chat_get_started_flutter).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation).
- You understand the number of chat groups and chat group members supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan).


## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK for Flutter to implement chat group features.

### Create a chat group

Set `ChatGroupStyle` and `inviteNeedConfirm` before creating a chat group.

1. Is the group public or private, and who can invite members (`ChatGroupStyle`):
- `PrivateOnlyOwnerInvite`: A private group. Only the chat group owner and admins can add users to the chat group.
- `PrivateMemberCanInvite`: A private group. All chat group members can add users to the chat group.
- `PublicJoinNeedApproval`: A public group. The chat group owner and admins can add users, and users can send join requests to the chat group.
- `PublicOpenJoin`: A public group. All users can join the chat group automatically without any need for approval from the chat group owner and admins.

2. Does a group invitation require consent from an invitee to add them to the group (`inviteNeedConfirm`):

- Yes (`ChatGroupOptions#inviteNeedConfirm` is set to `true`). After creating a group and sending group invitations, the subsequent logic varies based on whether an invitee automatically consents to the group invitation (`autoAcceptGroupInvitation`):
  - Yes (`autoAcceptGroupInvitation` is set to `true`). The invitee automatically joins the chat group and receives the `ChatGroupEventListener#onAutoAcceptInvitationFromGroup` callback, the chat group owner receives the `ChatGroupEventListener#onInvitationAcceptedFromGroup` and `ChatGroupEventListener#onMemberJoinedFromGroup` callbacks, and the other chat group members receives the `ChatGroupEventListener#onMemberJoinedFromGroup` callback.
  - No (`autoAcceptGroupInvitation` is set to `false`). The invitee receives the `ChatGroupEventListener#onInvitationReceivedFromGroup` callback and chooses whether to join the chat group:
    - If the invitee accepts the group invitation, the chat group owner receives the `ChatGroupEventListener#onInvitationAcceptedFromGroup` and `ChatGroupEventListener#onMemberJoinedFromGroup` callbacks, and the other chat group members receive the `ChatGroupEventListener#onMemberJoinedFromGroup` callback;
    - If the invitee declines the group invitation, the chat group owner receives the `ChatGroupEventListener#onInvitationDeclinedFromGroup` callback.

![](https://web-cdn.agora.io/docs-files/1653385689954)

- No (`ChatGroupOptions#inviteNeedConfirm` is set to `false`). After creating a chat group and sending group invitations, an invitee is added to the chat group regardless of their `autoAcceptGroupInvitation` setting. The invitee receives the `ChatGroupEventListener#onAutoAcceptInvitationFromGroup` callback, the chat group owner receives the `ChatGroupEventListener#onInvitationAcceptedFromGroup` and `ChatGroupEventListener#onMemberJoinedFromGroup` callbacks, and the other chat group members receive the `ChatGroupEventListener#onMemberJoinedFromGroup` callback.

Users can call `createGroup` to create a chat group and set the chat group attributes such as the chat group name, description, maximum number of members, and reason for creating the group, by specifying `ChatGroupOptions`.

The following code sample shows how to create a chat group:

```dart
ChatGroupOptions groupOptions = ChatGroupOptions(
  // The permission style of the chat group.
  style: ChatGroupStyle.PrivateMemberCanInvite,
  inviteNeedConfirm: true,
);
// The name of the chat group can be a maximum of 128 characters.
String groupName = "newGroup";
// The description of the chat group can be a maximum of 512 characters.
String groupDesc = "group desc";
try {
  await ChatClient.getInstance.groupManager.createGroup(
    groupName: groupName,
    desc: groupDesc,
    options: groupOptions,
  );
} on ChatError catch (e) {
}
```

### Destroy a chat group

Only the chat group owner can call `DestroyGroup` to disband a chat group. Once a chat group is disbanded, all chat group members receive the `OnDestroyedFromGroup` callback and are immediately removed from the chat group.

<div class="alert note"">Once a chat group is destroyed, all chat group data is deleted from the local database and memory.</div>

The following code sample shows how to destroy a chat group:

```dart
SDKClient.Instance.GroupManager.DestroyGroup(groupId, new CallBack(
  onSuccess: () =>
  {
  },
  onError: (code, desc) =>
  {
  }
));
```

### Join a chat group

The logic of joining a chat group varies according to the `GroupStyle` setting you choose when [creating the chat group](#create-a-chat-group):

- If the `GroupStyle` is set to `PublicOpenJoin`, all users can join the chat group without the consent from the chat group owner and admins. Once a user joins a chat group, all chat group members receive the `ChatGroupEventListener#onMemberJoinedFromGroup` callback;
- If the `GroupStyle` is set to `PublicJoinNeedApproval`, users can send join requests to the chat group. The chat group owner and chat group admins receive the `ChatGroupEventListener#onRequestToJoinReceivedFromGroup` callback and choose whether to accept the join request:
  - If the request is accepted, the user joins the chat group and receives the `ChatGroupEventListener#onRequestToJoinAcceptedFromGroup` callback, while all the other chat group members receive the `ChatGroupEventListener#onMemberJoinedFromGroup` callback.
  - If the request is declined, the user receives the `ChatGroupEventListener#onRequestToJoinDeclinedFromGroup` callback.

<div class="alert info">Users can only request to join public groups; private groups do not allow join requests.</div>

Users can refer to the following steps to join a chat group:

1. Call `fetchPublicGroupsFromServer` to retrieve the list of public groups from the server, and locate the ID of the chat group that you want to join.

2. Call `joinPublicGroup` to pass in the chat group ID and request to join the specified chat group.

The following code sample shows how to join a chat group:

```dart
// Retrieve the list of public groups with pagination.
try {
  ChatCursorResult<ChatGroupInfo> result =
      await ChatClient.getInstance.groupManager.fetchPublicGroupsFromServer();
} on ChatError catch (e) {
}
// Request to join the specified chat group.
try {
  await ChatClient.getInstance.groupManager.joinPublicGroup(groupId);
} on ChatError catch (e) {
}
```

### Leave a chat group

Chat group members can call `LeaveGroup` to leave the specified chat group, whereas the chat group owner cannot perform this operation. Once a member leaves a chat group, all the other chat group members receive the `ChatGroupEventListener#onMemberExitedFromGroup` callback.

The following code sample shows how to leave a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.leaveGroup(groupId);
} on ChatError catch (e) {
}
```

### Retrieve the attributes of a chat group

All chat group members can call `getGroupWithId` to retrieve the chat group attributes from memory. The attributes contain the chat group ID, name, description, owner, announcements, number of members, admin list, and whether to mute all members.

All chat group members can also call `fetchGroupInfoFromServer` to retrieve the chat group attributes from the server. The attributes contain the chat group ID, name, description, owner, announcements, number of members, admin list, and whether to mute all members.

The following code sample shows how to retrieve the chat group attributes:

```dart
// Retrieve the chat group attributes from memory.
try {
  ChatGroup? group = await ChatClient.getInstance.groupManager.getGroupWithId(groupId);
} on ChatError catch (e) {
}
// Retrieve the chat group attributes from the server.
try {
  ChatGroup group = await ChatClient.getInstance.groupManager.fetchGroupInfoFromServer(groupId);
} on ChatError catch (e) {
}
```

### Retrieve the chat group member list

All chat group members can call `fetchMemberListFromServer` to retrieve the chat group member list from the server with pagination.

The following code sample shows how to retrieve the chat group member list:

```dart
// The ID of the chat group.
try {
  ChatCursorResult<String> result =
      await ChatClient.getInstance.groupManager.fetchMemberListFromServer(
    groupId,
  );
} on ChatError catch (e) {
}
```

### Retrieve the chat group list

Users can call `fetchJoinedGroupsFromServer` to retrieve the joined chat group list from the server with pagination, as shown in the following code sample:

```dart
try {
  List<ChatGroup> list =
      await ChatClient.getInstance.groupManager.fetchJoinedGroupsFromServer();
} on ChatError catch (e) {
}
```

Users can call `getJoinedGroups` to retrieve the joined chat group list from the local database. To ensure the accuracy of results, retrieve the joined chat group list from the server first. The code sample is as follows:

```dart
try {
  List<ChatGroup> list =
      await ChatClient.getInstance.groupManager.getJoinedGroups();
} on ChatError catch (e) {
}
```

Users can also call `fetchPublicGroupsFromServer` to retrieve public chat group list from the server with pagination, as shown in the following code sample:

```dart
try {
  ChatCursorResult<ChatGroupInfo> result =
      await ChatClient.getInstance.groupManager.fetchPublicGroupsFromServer(
    // The maximum number of chat groups to retrieve with pagination.
    pageSize: pageSize,
    // The page number from which to start getting data.
    cursor: cursor,
  );
} on ChatError catch (e) {
}
```

### Block and unblock a chat group

#### Block a chat group

Chat group members can call `blockGroup` to block a chat group, whereas the chat group owner and admins cannot perform this operation. Once a member blocks a chat group, this member can no longer receive messages from the chat group.

The following code sample shows how to block a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.blockGroup(groupId);
} on ChatError catch (e) {
}
```

#### Unblock a chat group

Chat group members can call `unblockGroup` to unblock a chat group.

The following code sample shows how to unblock a chat group:

```dart
try {
  await ChatClient.getInstance.groupManager.unblockGroup(groupId);
} on ChatError catch (e) {
}
```

#### Check whether a user blocks a chat group

All chat group members can call `fetchGroupInfoFromServer` to check whether they block a chat group according to the `ChatGroup#messageBlocked` field.

The following code sample shows how to check whether a user blocks a chat group:

```dart
try {
  ChatGroup group = await ChatClient.getInstance.groupManager
      .fetchGroupInfoFromServer(groupId);
  // Check whether a user blocks a chat group.
  if (group.messageBlocked == true) {
  }
} on ChatError catch (e) {
}
```

### Listen for chat group events

To monitor the chat group events, users can listen for the callbacks in the `ChatGroupEventListener` class and add app logics accordingly. If a user wants to stop listening for the callbacks, make sure that the user removes the listener to prevent memory leakage.

Refer to the following code sample to listen for chat group events:

```dart
// Inherit and implement the `ChatGroupEventListener` class.
class _GroupPageState extends State<GroupPage> implements ChatGroupEventListener {
  @override
  void initState() {
    // Add the chat group listener.
    ChatClient.getInstance.groupManager.addGroupChangeListener(this);
    super.initState();
  }
  @override
  void dispose() {
    // Remove the chat group listener.
    ChatClient.getInstance.groupManager.removeGroupChangeListener(this);
    super.dispose();
  }
  @override
  Widget build(BuildContext context) {
    return Container();
  }
  @override
  // Occurs when a chat group member is promoted to an admin.
  void onAdminAddedFromGroup(
    String groupId,
    String admin,
  ) {}
  @override
  // Occurs when a chat group admin is demoted to a regular member.
  void onAdminRemovedFromGroup(
    String groupId,
    String admin,
  ) {}
  @override
  // Occurs when all chat group members are muted or unmuted.
  void onAllGroupMemberMuteStateChanged(
    String groupId,
    bool isAllMuted,
  ) {}
  @override
  // Occurs when the chat group announcements are updated.
  void onAnnouncementChangedFromGroup(
    String groupId,
    String announcement,
  ) {}
  @override
  // Occurs when a user automatically accepts a chat group invitation.
  void onAutoAcceptInvitationFromGroup(
    String groupId,
    String inviter,
    String? inviteMessage,
  ) {}
  @override
  // Occurs when a chat group is destroyed.
  void onGroupDestroyed(
    String groupId,
    String? groupName,
  ) {}
  @override
  // Occurs when a user accepts a group invitation.
  void onInvitationAcceptedFromGroup(
    String groupId,
    String invitee,
    String? reason,
  ) {}
  @override
  // Occurs when a user declines a group invitation.
  void onInvitationDeclinedFromGroup(
    String groupId,
    String invitee,
    String? reason,
  ) {}
  @override
  // Occurs when a user receives a group invitation.
  void onInvitationReceivedFromGroup(
    String groupId,
    String? groupName,
    String inviter,
    String? reason,
  ) {}
  @override
  // Occurs when a member leaves a chat group.
  void onMemberExitedFromGroup(
    String groupId,
    String member,
  ) {}
  @override
  // Occurs when a user joins a chat group.
  void onMemberJoinedFromGroup(
    String groupId,
    String member,
  ) {}
  @override
  // Occurs when a member is added to the chat group mute list.
  void onMuteListAddedFromGroup(
    String groupId,
    List<String> mutes,
    int? muteExpire,
  ) {}
  @override
  // Occurs when a member is removed from the chat group mute list.
  void onMuteListRemovedFromGroup(
    String groupId,
    List<String> mutes,
  ) {}
  @override
  // Occurs when the chat group owner is changed.
  void onOwnerChangedFromGroup(
    String groupId,
    String newOwner,
    String oldOwner,
  ) {}
  @override
  // Occurs when the chat group owner and chat group admins approve a join request.
  void onRequestToJoinAcceptedFromGroup(
    String groupId,
    String? groupName,
    String accepter,
  ) {}
  @override
  // Occurs when the chat group owner and chat group admins decline a join request.
  void onRequestToJoinDeclinedFromGroup(
    String groupId,
    String? groupName,
    String decliner,
    String? reason,
  ) {}
  @override
  // Occurs when the chat group owner and chat group admins receive a join request.
  void onRequestToJoinReceivedFromGroup(
    String groupId,
    String? groupName,
    String applicant,
    String? reason,
  ) {}
  @override
  // Occurs when a shared file is uploaded to a chat group.
  void onSharedFileAddedFromGroup(
    String groupId,
    ChatGroupSharedFile sharedFile,
  ) {}
  @override
  // Occurs when a shared file is deleted in a chat group.
  void onSharedFileDeletedFromGroup(
    String groupId,
    String fileId,
  ) {}
  @override
  // Occurs when a member is removed from a chat group.
  void onUserRemovedFromGroup(
    String groupId,
    String? groupName,
  ) {}
  @override
  // Occurs when a member is added to the chat group allow list.
  void onWhiteListAddedFromGroup(
    String groupId,
    List<String> members,
  ) {}
  @override
  // Occurs when a member is removed from the chat group allow list.
  void onWhiteListRemovedFromGroup(
    String groupId,
    List<String> members,
  ) {}
}
```