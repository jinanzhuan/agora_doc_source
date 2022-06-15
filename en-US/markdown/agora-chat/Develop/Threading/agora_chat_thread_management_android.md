# Thread Management

Threads enable users to create a separate conversation from a specific message within a chat group to keep the main chat uncluttered.

The following illustration shows the implementation of creating a thread, a conversation in a thread, and the operations you can perform in a thread.

![](https://web-cdn.agora.io/docs-files/1655176216910)

This page shows how to use the Agora Chat SDK to create and manage threads in your app.


## Understand the tech

The Agora Chat SDK provides the `ChatThreadManager`, `ChatThread`, `ChatThreadChangeListener`, and `ChatThreadEvent` classes for thread management, which allows you to implement the following features:

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

- You have initialized the Agora Chat SDK. For details, see [Get Started with Android](./agora_chat_get_started_android?platform=Android).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation?platform=Android).
- You understand the number of threads and thread members supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan?platform=Android).
- You have contacted support@agora.io to activate the threading feature.

## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK to implement thread features.

### Create a thread

All chat group members can call `createChatThread` to create a thread from a specific message in a chat group.

Once a thread is created in a chat group, all chat group members receive the `ChatThreadChangeListener#onChatThreadCreated` callback. In a multi-device scenario, all the other devices receive the `MultiDeviceListener#onThreadEvent` callback triggered by the `THREAD_CREATE` event.

The following code sample shows how to create a thread in a chat group:

```java
// parentId: The ID of a chat group where a thread resides.
// messageId: The ID of a message, from which a thread is created.
// threadName: The name of a thread. The maximum length of a thread name is 64 characters.
ChatClient.getInstance().chatThreadManager().createChatThread(parentId, messageId, threadName, new ValueCallBack<ChatThread>() {
    @Override
    public void onSuccess(ChatThread value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
        
    }
});
```

### Destroy a thread

Only the chat group owner and admins can call `destroyChatThread` to disband a thread in a chat group.

Once a thread is disbanded, all chat group members receive the `ChatThreadChangeListener#onChatThreadDestroyed` callback. In a multi-device scenario, all the other devices receive the `MultiDeviceListener#onThreadEvent` callback triggered by the `THREAD_DESTROY` event.

<div class="alert note">Once a thread is destroyed or the chat group where a thread resides is destroyed, all data of the thread is deleted from the local database and memory.</div>

The following code sample shows how to destroy a thread:

```java
ChatClient.getInstance().chatThreadManager().destroyChatThread(chatThreadId, new CallBack() {
    @Override
    public void onSuccess() {
        
    }
    @Override
    public void onError(int code, String error) {
    }
});
```

### Join a thread

All chat group members can refer to the following steps to join a thread:

1. Use either of the following two approaches to retrieve the thread ID:
- Retrieve the thread list in a chat group by calling `getChatThreadsFromServer`, and locate the ID of the thread that you want to join.
- Retrieve the thread ID within the `ChatThreadChangeListener#onChatThreadCreated` and `ChatThreadChangeListener#onChatThreadUpdated` callbacks that you receive.

2. Call `joinChatThread` to pass in the thread ID and join the specified thread.

In a multi-device scenario, all the other devices receive the `MultiDeviceListener#onThreadEvent` callback triggered by the `THREAD_JOIN` event.

The following code sample shows how to join a thread:

```java
ChatClient.getInstance().chatThreadManager().joinChatThread(chatThreadId, new ValueCallBack<ChatThread>() {
    @Override
    public void onSuccess(ChatThread value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

### Leave a thread

All thread members can call `leaveChatThread` to leave a thread. Once a member leaves a thread, they can no longer receive the thread messages.

In a multi-device scenario, all the other devices receive the `MultiDeviceListener#onThreadEvent` callback triggered by the `THREAD_LEAVE` event.

The following code sample shows how to leave a thread:

```java
ChatClient.getInstance().chatThreadManager().leaveChatThread(chatThreadId, new CallBack() {
    @Override
    public void onSuccess() {
        
    }
    @Override
    public void onError(int code, String error) {
    }
});
```

### Remove a member from a thread

Only the chat group owner and admins can call `removeMemberFromChatThread` to remove the specified member from a thread.

Once a member is removed from a thread, they receive the `ChatThreadChangeListener#onChatThreadUserRemoved` callback and can no longer receive the thread messages.

The following code sample shows how to remove a member from a thread:

```java
// chatThreadId: : The ID of a thread.
// member: The ID of the user to be removed from a thread.
ChatClient.getInstance().chatThreadManager().removeMemberFromChatThread(chatThreadId, member, 
        new CallBack() {
    @Override
    public void onSuccess() {
        
    }
    @Override
    public void onError(int code, String error) {
    }
});
```

### Update the name of a thread

Only the chat group owner, chat group admins, and thread creator can call `updateChatThreadName` to update a thread name.

Once a thread name is updated, all chat group members receive the `ChatThreadChangeListener#onChatThreadUpdated` callback. In a multi-device scenario, all the other devices receive the `MultiDeviceListener#onThreadEvent` callback triggered by the `THREAD_UPDATE` event.

The following code sample shows how to update a thread name:

```java
// chatThreadId: The ID of a thread.
// newChatThreadName: The updated thread name. The maximum length of a thread name is 64 characters.
ChatClient.getInstance().chatThreadManager().updateChatThreadName(chatThreadId, newChatThreadName, 
        new CallBack() {
    @Override
    public void onSuccess() {
        
    }
    @Override
    public void onError(int code, String error) {
    }
});
```

### Retrieve the attributes of a thread

All chat group members can call `getChatThreadFromServer` to retrieve the thread attributes from the server.

The following code sample shows how to retrieve the thread attributes:

```java
// chatThreadID: The thread ID.
ChatClient.getInstance().chatThreadManager().getChatThreadFromServer(chatThreadId, new ValueCallBack<ChatThread>() {
    @Override
    public void onSuccess(ChatThread value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

### Retrieve the member list of a thread

All chat group members can call `getChatThreadMembers` to retrieve the paginated member list of a thread from the server.

```java
// chatThreadId: The thread ID.
// limit: The maximum number of members to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance().chatThreadManager().getChatThreadMembers(chatThreadId, limit, cursor, 
        new ValueCallBack<CursorResult<String>>() {
    @Override
    public void onSuccess(CursorResult<String> value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

### Retrieve a thread list

Users can call `getJoinedChatThreadsFromServer` to retrieve a paginated list of all the threads they have joined from the server, as shown in the following code sample:

```java
// limit: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call.
ChatClient.getInstance().chatThreadManager().getJoinedChatThreadsFromServer(limit, cursor, 
        new ValueCallBack<CursorResult<ChatThread>>() {
    @Override
    public void onSuccess(CursorResult<ChatThread> value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

Users can call `getJoinedChatThreadsFromServer` to retrieve a paginated list of all the threads they have joined in a specified chat group from the server, as shown in the following code sample:

```java
// parentId: The chat group ID.
// limit: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call. 
ChatClient.getInstance().chatThreadManager().getJoinedChatThreadsFromServer(parentId, limit, cursor, 
        new ValueCallBack<CursorResult<ChatThread>>() {
    @Override
    public void onSuccess(CursorResult<ChatThread> value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

Users can also call `getChatThreadsFromServer` to retrieve a paginated list of all the threads in a specified chat group from the server, as shown in the following code sample:

```java
// parentId: The chat group ID.
// limit: The maximum number of threads to retrieve per page. The range is [1, 50].
// cursor: The position from which to start getting data. Pass in `null` or an empty string at the first call. 
ChatClient.getInstance().chatThreadManager().getChatThreadsFromServer(parentId, limit, cursor, 
        new ValueCallBack<CursorResult<ChatThread>>() {
    @Override
    public void onSuccess(CursorResult<ChatThread> value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

### Retrieve the latest message from multiple threads

Users can call `getChatThreadLatestMessage` to retrieve the latest message from multiple threads.

The following code sample shows how to retrieve the latest message from multiple threads:

```java
// chatThreadIdList: The thread IDs. You can pass in a maximum of 20 thread IDs.
ChatClient.getInstance().chatThreadManager().getChatThreadLatestMessage(chatThreadIdList, 
        new ValueCallBack<Map<String, ChatMessage>>() {
    @Override
    public void onSuccess(Map<String, ChatMessage> value) {
        
    }
    @Override
    public void onError(int error, String errorMsg) {
    }
});
```

### Listen for thread events

To monitor the thread events, users can listen for the callbacks in the `ChatThreadManager` class and add app logics accordingly. If a user wants to stop listening for the callbacks, make sure that the user removes the listener to prevent memory leakage.

Refer to the following code sample to listen for thread events:

```java
ChatThreadChangeListener chatThreadChangeListener = new ChatThreadChangeListener() {
    @Override
    // Occurs when a thread is created.
    public void onChatThreadCreated(ChatThreadEvent event) {}
    @Override
    // Occurs when a thread has a new message, a thread name is updated, or a thread message is recalled.
    public void onChatThreadUpdated(ChatThreadEvent event) {}
    // Occurs when a thread is destroyed.
    @Override
    public void onChatThreadDestroyed(ChatThreadEvent event) {}
    // Occurs when a member is removed from a thread.
    @Override
    public void onChatThreadUserRemoved(ChatThreadEvent event) {}
};
// Add the thread listener.
ChatClient.getInstance().chatThreadManager().addChatThreadChangeListener(chatThreadChangeListener);
// Remove the thread listener.
ChatClient.getInstance().chatThreadManager().removeChatThreadChangeListener(chatThreadChangeListener);
```