# Thread Messages

Threads enable users to create a separate conversation from a specific message within a chat group to keep the main chat uncluttered.

This page shows how to use the Agora Chat SDK to send, receive, recall, and retrieve thread messages in your app.

## Understand the tech

The Agora Chat SDK provides the `ChatManager`, `ChatMessage`, and `ChatThread` classes for thread messages, which allows you to implement the following features:

- Send a thread message
- Receive a thread message
- Recall a thread message
- Retrieve thread messages

~338e0e30-e568-11ec-8e95-1b7dfd4b7cb0~


## Prerequisites

Before proceeding, ensure that you meet the following requirements:

- You have initialized the Agora Chat SDK. For details, see [Get Started with Android](./agora_chat_get_started_android?platform=Android).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation?platform=Android).
- You understand the number of threads and thread members supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan?platform=Android).


## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK to implement thread features.

### Send a thread message

Sending a thread message is similar to sending a message in a chat group. The difference lies in the `isChatThreadMessage` field, as shown in the following code sample:

```java
// Calls `createTxtSendMessage` to create a text message. 
// Sets `content` to the content of the text message.
// Sets `chatThreadId` to the ID of a thread that receives the text message.
ChatMessage message = ChatMessage.createTxtSendMessage(content, chatThreadId); 
// Sets `ChatType` to `GroupChat` as a thread that belongs to a chat group.
message.setChatType(ChatType.GroupChat); 
// Sets `isChatThreadMessage` to `true` to mark this message as a thread message.
message.setisChatThreadMessage(true);
// Calls `setMessageStatusCallback` to listen for the message sending status. You can implement subsequent settings in this callback, for example, displaying a pop-up if the message sending fails.
message.setMessageStatusCallback(new CallBack() {
     @Override
     public void onSuccess() {
        showToast("The message is successfully sent");
        dialog.dismiss();
     }
     @Override
     public void onError(int code, String error) {
         showToast("Failed to send the message");
     }
     @Override
     public void onProgress(int progress, String status) {
     }
});
// Calls `sendMessage` to send the text message.
ChatClient.getInstance().chatManager().sendMessage(message);
```

For more information about sending a message, see [Send Messages](./agora_chat_message_android?platform=Android#send-and-receive-messages).


### Receive a thread message

Once a thread has a new message, all chat group members receive the `ChatThreadChangeListener#onChatThreadUpdated` callback. Thread members can also listen for the `MessageListener#onMessageReceived` callback to receive thread messages, as shown in the following code sample:

```java
MessageListener msgListener = new MessageListener() {
   // The SDK triggers the `onMessageReceived` callback when it receives a message.
   // After receiving this callback, the SDK parses the message and displays it.
   @Override
   public void onMessageReceived(List<ChatMessage> messages) {
       for (ChatMessage message : messages) {
           if(message.isChatThreadMessage()) {
               // You can implement subsequent settings in this callback.
           }
       }
   }
   ...
};
// Calls `addMessageListener` to add a message listener.
ChatClient.getInstance().chatManager().addMessageListener(msgListener);
// Calls `removeMessageListener` to remove the message listener.
ChatClient.getInstance().chatManager().removeMessageListener(msgListener);
```

For more information about receiving a message, see [Receive Messages](./agora_chat_message_android?platform=Android#send-and-receive-messages).


### Recall a thread message

For details about how to recall a message, refer to [Recall Messages](./agora_chat_message_android?platform=Android#recall-messages).

Once a message is recalled in a thread, all chat group members receive the `ChatThreadChangeListener#onChatThreadUpdated` callback. Thread members can also listen for the `MessageListener#onMessageRecalled` callback, as shown in the following code sample:

```java
MessageListener msgListener = new MessageListener() {
   // The SDK triggers the `onMessageRecalled` callback when it recalls a message.
   // After receiving this callback, the SDK parses the message and updates its display.
   @Override
   public void onMessageRecalled(List<ChatMessage> messages) {
       for (ChatMessage message : messages) {
           if(message.isChatThreadMessage()) {
               // You can implement subsequent settings in this callback.
           }
       }
   }
   ...
};
```

### Retrieve thread messages

Whether to retrieve thread messages from the server or local database depends on your production context.

When you join a thread, messages are displayed in chronological order by default. In this case, Agora recommends that you retrieve the historical messages of the thread from the server. When you recall a thread message, Agora recommend that you insert the recall notification in the local database.

#### Retrieve thread messages from the server

For details about how to retrieve messages from the server, see [Retrieve Historical Messages](./agora_chat_message_android?platform=Android#retrieve-historical-messages-from-the-server).

#### Retrieve the conversation of a thread from the memory and local database

By calling [`getAllConversations`](./agora_chat_message_android#retrieve-local-conversations), you can only retrieve the conversations of one-to-one chats or group chats. To retrieve the conversation of a thread, refer to the following code sample:

```java
// Sets the conversation type to group chat as a thread belongs to a chat group.
// Sets `isChatThread` to `true` to mark the conversation as a thread.
ChatConversation conversation = ChatClient.getInstance().chatManager().getConversation(chatThreadId, ChatConversationType.GroupChat, createIfNotExists, isChatThread);
// Retrieves all messages in the specified thread from the memory.
List<ChatMessage> messages = conversation.getAllMessages();
// Retrieves more messages in the specified thread from the local database. The SDK automatically loads and stores the retrieved messages to the memory.
List<ChatMessage> messages = conversation.loadMoreMsgFromDB(startMsgId, pagesize, searchDirection);
```