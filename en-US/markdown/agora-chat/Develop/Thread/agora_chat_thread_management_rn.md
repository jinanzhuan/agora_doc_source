# Thread Management

Threads enable users to create a separate conversation from a specific message within a chat group to keep the main chat uncluttered.

The following illustration shows the implementation of creating a thread, a conversation in a thread, and the operations you can perform in a thread:

![](https://web-cdn.agora.io/docs-files/1655176216910)

This page shows how to use the Agora Chat SDK to create and manage threads in your app.


## Understand the tech

The Agora Chat SDK provides the `ChatManager`, `ChatMessageThread`, `ChatMessageEventListener`, and `ChatMessageThreadEvent` classes for thread management, which allows you to implement the following features:

- Create and destroy a thread
- Join and leave a thread
- Remove a member from a thread
- Update the name of a thread
- Retrieve the attributes of a thread
- Retrieve the member list of a thread
- Retrieve a thread list
- Retrieve the latest message from multiple threads
- Listen for thread events


## Prerequisites

Before proceeding, ensure that you meet the following requirements:

- You have initialized the Agora Chat SDK. For details, see [Get Started with React Native](./agora_chat_get_started_rn).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation).
- You understand the number of threads and thread members supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan).


## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK to implement thread features.

### Create a thread

All chat group members can call `createChatThread` to create a thread from a specific message in a chat group.

Once a thread is created in a chat group, all chat group members receive the `ChatMessageEventListener#onChatMessageThreadCreated` callback. In a multi-device scenario, all the other devices receive the `ChatMultiDeviceEventListener#onThreadEvent` callback triggered by the `THREAD_CREATE` event.

The following code sample shows how to create a thread in a chat group:

```typescript
// name: The name of a thread. The maximum length of a thread name is 64 characters.
// msgId: The ID of a message, from which a thread is created.
// parentId: The ID of a chat group where a thread resides.
ChatClient.getInstance()
  .chatManager.createChatThread(name, msgId, parentId)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Destroy a thread

Only the chat group owner and admins can call `destroyChatThread` to disband a thread in a chat group.

Once a thread is disbanded, all chat group members receive the `ChatMessageEventListener#onChatMessageThreadDestroyed` callback. In a multi-device scenario, all the other devices receive the `ChatMultiDeviceEventListener#onThreadEvent` callback triggered by the `THREAD_DESTROY` event.

<div class="alert note">Once a thread is destroyed or the chat group where a thread resides is destroyed, all data of the thread is deleted from the local database and memory.</div>

The following code sample shows how to destroy a thread:

```typescript
// chatThreadID: The ID of a thread.
ChatClient.getInstance()
  .chatManager.destroyChatThread(chatThreadID)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Join a thread

All chat group members can refer to the following steps to join a thread:

1. Use either of the following two approaches to retrieve the thread ID:
- Retrieve the thread list in a chat group by calling [`fetchChatThreadWithParentFromServer`](#fetch), and locate the ID of the thread that you want to join.
- Retrieve the thread ID within the `ChatMessageEventListener#onChatMessageThreadCreated` and `ChatMessageEventListener#onChatMessageThreadUpdated` callbacks that you receive.

2. Call `joinChatThread` to pass in the thread ID and join the specified thread.

In a multi-device scenario, all the other devices receive the `ChatMultiDeviceEventListener#onThreadEvent` callback triggered by the `THREAD_JOIN` event.

The following code sample shows how to join a thread:

```typescript
// chatThreadID: The ID of a thread.
ChatClient.getInstance()
  .chatManager.joinChatThread(chatThreadID)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Leave a thread

All thread members can call `leaveChatThread` to leave a thread. Once a member leaves a thread, they can no longer receive the thread messages.

In a multi-device scenario, all the other devices receive the `ChatMultiDeviceEventListener#onThreadEvent` callback triggered by the `THREAD_LEAVE` event.

The following code sample shows how to leave a thread:

```typescript
// chatThreadID: The ID of a thread.
ChatClient.getInstance()
  .chatManager.leaveChatThread(chatThreadID)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Remove a member from a thread

Only the chat group owner and admins can call `removeMemberWithChatThread` to remove the specified member from a thread.

Once a member is removed from a thread, they receive the `ChatMessageEventListener#onUserRemoved` callback and can no longer receive the thread messages.

The following code sample shows how to remove a member from a thread:

```typescript
// chatThreadID: The ID of a thread.
// member: The ID of the user to be removed from a thread.
ChatClient.getInstance()
  .chatManager.removeMemberWithChatThread(chatThreadID, memberId)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Update the name of a thread

Only the chat group owner, chat group admins, and thread creator can call `updateChatThreadName` to update a thread name.

Once a thread name is updated, all chat group members receive the `ChatMessageEventListener#onChatMessageThreadUpdated` callback. In a multi-device scenario, all the other devices receive the `ChatMultiDeviceEventListener#onThreadEvent` callback triggered by the `THREAD_UPDATE` event.

The following code sample shows how to update a thread name:

```typescript
// chatThreadID: The ID of a thread.
// newChatThreadName: The updated thread name. The maximum length of a thread name is 64 characters.
ChatClient.getInstance()
  .chatManager.updateChatThreadName(chatThreadID, newName)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Retrieve the attributes of a thread

All chat group members can call `fetchChatThreadFromServer` to retrieve the thread attributes from the server.

The following code sample shows how to retrieve the thread attributes:

```typescript
// chatThreadID: The ID of a thread.
ChatClient.getInstance()
  .chatManager.fetchChatThreadFromServer(chatThreadID)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Retrieve the member list of a thread

All chat group members can call `fetchMembersWithChatThreadFromServer` to retrieve a paginated member list of a thread from the server, as shown in the following code sample:

```typescript
// chatThreadId: The ID of a thread.
// pageSize: The maximum number of members to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance()
  .chatManager.fetchMembersWithChatThreadFromServer(
    chatThreadID,
    cursor,
    pageSize
  )
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Retrieve a thread list

Users can call `fetchJoinedChatThreadFromServer` to retrieve a paginated list from the server of all the threads they have joined, as shown in the following code sample:

```typescript
// pageSize: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance()
  .chatManager.fetchJoinedChatThreadFromServer(cursor, pageSize)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

Users can call `fetchJoinedChatThreadWithParentFromServer` to retrieve a paginated list from the server of all the threads they have joined in a specified chat group, as shown in the following code sample:

```typescript
// parentId: The chat group ID.
// pageSize: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance()
  .chatManager.fetchJoinedChatThreadWithParentFromServer(
    parentId,
    cursor,
    pageSize
  )
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

Users can also call `fetchChatThreadWithParentFromServer` to retrieve a paginated list from the server of all the threads in a specified chat group, as shown in the following code sample:<a name="fetch"></a>

```typescript
// parentId: The chat group ID.
// pageSize: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance()
  .chatManager.fetchChatThreadWithParentFromServer(parentId, cursor, pageSize)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Retrieve the latest message from multiple threads

Users can call `fetchLastMessageWithChatThread` to retrieve the latest message from multiple threads.

The following code sample shows how to retrieve the latest message from multiple threads:

```typescript
// chatThreadIDs: The thread IDs. You can pass in a maximum of 20 thread IDs at each call.
ChatClient.getInstance()
  .chatManager.fetchLastMessageWithChatThread(chatThreadIDs)
  .then((result) => {
    console.log("success: ", result);
  })
  .catch((error) => {
    console.log("fail: ", error);
  });
```

### Listen for thread events

To monitor thread events, users can listen for the callbacks in the `ChatManager` class and add app logics accordingly. If a user wants to stop listening for the callbacks, make sure that the user removes the listener to prevent memory leakage.

Refer to the following code sample to listen for thread events:

```typescript
// Inherits and implements ChatMessageEventListener.
class ChatMessageEvent implements ChatMessageEventListener {
  // Occurs when a thread is created.
  onChatMessageThreadCreated(msgThread: 
  ChatMessageThreadEvent): void {
    console.log(`onChatMessageThreadCreated: `, msgThread);
  }
  // Occurs when a thread has a new message, a thread name is updated, or a thread message is recalled.
  onChatMessageThreadUpdated(msgThread: ChatMessageThreadEvent): void {
    console.log(`onChatMessageThreadUpdated: `, msgThread);
  }
  // Occurs when a thread is destroyed.
  onChatMessageThreadDestroyed(msgThread: ChatMessageThreadEvent): void {
    console.log(`onChatMessageThreadDestroyed: `, msgThread);
  }
  // Occurs when a member is removed from a thread.
  onChatMessageThreadUserRemoved(msgThread: ChatMessageThreadEvent): void {
    console.log(`onChatMessageThreadUserRemoved: `, msgThread);
  }
}
const listener = new ChatMessageEvent();
// Adds the message listener.
ChatClient.getInstance().chatManager.addMessageListener(listener);
// Removes the message listener.
ChatClient.getInstance().chatManager.removeMessageListener(listener);
// Removes all the message listeners.
ChatClient.getInstance().chatManager.removeAllMessageListener();
```