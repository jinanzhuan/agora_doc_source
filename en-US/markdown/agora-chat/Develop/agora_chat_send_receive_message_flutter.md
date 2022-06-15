After logging in to Agora Chat, users can send the following types of meesages to a peer user, a chat group, or a chat room:
- Text messages, including hyperlinks and emojis.
- Attachment messages, including image, voice, video, and file messages.
- Location messages.
- CMD messages.
- Extended messages.
- Custom messages.

This page shows how to implement sending and receiving these messages using the Agora Chat SDK.

## Understand the tech

The Agora Chat SDK for Unity uses the `ChatMessage` and `ChatMessage` classes to send, receive, and withdraw messages.

The process of sending and receiving a message is as follows:

1. The message sender creates a text, file, or attachment message using the corresponding `Create` method.
2. The message sender calls `sendMessage` to send the message.
3. The message recipient listens for `ChatManagerListener` and receives the message in the `onMessagesReceived` callback.

## Prerequistes

Before proceeding, ensure that you meet the following requirements:
- You have integrated the Agora Chat SDK, initialized the SDK and implemented the functionality of registering accounts and login. For details, see [Get Started with Agora Chat](./agora_chat_get_started_flutter?platform=Flutter).
- You understand the [API call frequency limits](/agora_chat_limitation?platform=Flutter).

## Implementation

This section shows how to implement sending and receiving the various types of messages.

### Send a message

Follow the steps to create and send a message, and listen for the result of sending the message.

1. Use the `ChatMessage` class to create a message.

  ```dart
  // Sets the message type. Agora Chat supports 8 message types.
  MessageType messageType = MessageType.TXT;
  // Sets the user ID of the recipient.
  String targetId = "tom";
  // Sets the chat type. You can set it as a peer user, chat group, or chat room.
  ChatType chatType = ChatType.Chat;
  // Creates a message. For different message types, you need to set different parameters.
  // Creates a text message.
  ChatMessage txtMsg = ChatMessage.createTxtSendMessage(
    username: targetId,
    content: "This is text message",
  );
  // Creates an image message. You need to set the local path, length, and width of the image file, and the image name displayed on the UI.
  String imgPath = "/data/.../image.jpg";
  double imgWidth = 100;
  double imgHeight = 100;
  String imgName = "image.jpg";
  int imgSize = 3000;
  ChatMessage imgMessage = ChatMessage.createImageSendMessage(
    username: targetId,
    filePath: imgPath,
    width: imgWidth,
    height: imgHeight,
    displayName: imgName,
    fileSize: imgSize,
  );
  // Creates a CMD message. A CMD message contains a command for the recipient to take action. You can customize the command.
  String action = "writing";
  ChatMessage cmdMsg = ChatMessage.createCmdSendMessage(
    username: targetId,
    action: action,
  );
  // Creates a custom message. A custom message contains the message event type and the extension field.
  // You can customize the extension field according to your use case.
  String event = "gift";
  Map<String, String> params = {"key": "value"};
  ChatMessage customMsg = ChatMessage.createCustomSendMessage(
    username: targetId,
    event: event,
    params: params,
  );
  // Creates a file message. A file message contains the local file path and the name displayed on the UI.
  String filePath = "data/.../foo.zip";
  String fileName = "foo.zip";
  int fileSize = 6000;
  ChatMessage fileMsg = ChatMessage.createFileSendMessage(
    username: targetId,
    filePath: filePath,
    displayName: fileName,
    fileSize: fileSize,
  );
  // Creates a location message. You need to set the longitude and latitude information of the location, as well as the name of the location.
  // To send a location message, you need to integrate a third-party map service provider to get the longitude and latitude information of the location. When the recipient receives the location information, the service provider renders the location on the map according to the longitude and latitude information.
  double latitude = 114.78;
  double longitude = 39.89;
  String address = "darwin";
  ChatMessage.createLocationSendMessage(
    username: targetId,
    latitude: latitude,
    longitude: longitude,
    address: address,
  );
  // Creates a video message. A video message includes two attachment files, including the video file and the thumbnail of the video. The SDK uses the first frame of the video as the thumbnail. You also need to set the local path, length, width, display name, and duration of the video file.
  String videoPath = "data/.../foo.mp4";
  double videoWidth = 100;
  double videoHeight = 100;
  String videoName = "foo.mp4";
  int videoDuration = 5;
  int videoSize = 4000;
  ChatMessage.createVideoSendMessage(
    username: targetId,
    filePath: videoPath,
    width: videoWidth,
    height: videoHeight,
    duration: videoDuration,
    fileSize: videoSize,
    displayName: videoName,
  );
  // Creates a voice message. A voice message contains the local path, display name, and duration of the audio file.
  String voicePath = "data/.../foo.wav";
  String voiceName = "foo.wav";
  int voiceDuration = 5;
  int voiceSize = 1000;
  ChatMessage.createVoiceSendMessage(
    username: targetId,
    filePath: voicePath,
    duration: voiceDuration,
    fileSize: voiceSize,
    displayName: voiceName,
  );
  ChatMessage message =
      ChatMessage.createCmdSendMessage(username: targetId, action: "action");
  ```

2. Send the message using `sendMessage` in `ChatManager`.

  ```dart
  ChatClient.getInstance.chatManager.sendMessage(message).then((value) {
  });
  ```

  To get the progress and result of the message sending, you need to implement `MessageStatusCallBack`. For CMD messages which do not necessarily need a result, you do not need to set the callback.

  ```dart
  // Implements callbacks for getting the message status.
  message.setMessageStatusCallBack(
    MessageStatusCallBack(
      onSuccess: () {
        // Occurs when the message sending succeeds. You can update the message and add other operations in this callback.
      },
      onError: (error) {
        // Occurs when the message sending fails. You can update the message status and add other operations in this callback.
      },
      onProgress: (progress) {
        // For attachment messages such as image, voice, file, and video, you can get a progress value for uploading or downloading them in this callback.
      },
    ),
  );
  // The result of the messaging sending is returned in the callback. The return value of the method only indicates the result of the message call.
  ChatClient.getInstance.chatManager.sendMessage(message).then((value) {
    // Finish sending the message.
  });
  ```

### Receive the message

You can use `ChatManagerListener` to listen for message events. You can add multiple `ChatManagerListener` objects to listen for multiple events. When you no longer listen for an event, for example, when you call `dispose`, ensure that you remove the object.

When a message arrives, the recipient recieves an `onMessgesReceived` callback. Each callback contains one or more messages. You can traverse the message list, and parse and render these messages in this callback.

```dart
// Inherits and implements ChatManagerListener
class _ChatMessagesPageState extends State<ChatMessagesPage>
    implements ChatManagerListener {
  @override
  void initState() {
    super.initState();
    // Adds a chat event listener.
    ChatClient.getInstance.chatManager.addChatManagerListener(this);
  }
  @override
  Widget build(BuildContext context) {
    return Container();
  }
  @override
  void dispose() {
    // Removes the event listener.
    ChatClient.getInstance.chatManager.removeChatManagerListener(this);
    super.dispose();
  }
  @override
  void messageReactionDidChange(List<ChatMessageReactionChange> list) {}
  @override
  void onCmdMessagesReceived(List<ChatMessage> messages) {}
  @override
  void onConversationRead(String from, String to) {}
  @override
  void onConversationsUpdate() {}
  @override
  void onGroupMessageRead(List<ChatGroupMessageAck> groupMessageAcks) {}
  @override
  void onMessagesDelivered(List<ChatMessage> messages) {}
  @override
  void onMessagesRead(List<ChatMessage> messages) {}
  @override
  void onMessagesRecalled(List<ChatMessage> messages) {}
  @override
  void onMessagesReceived(List<ChatMessage> messages) {}
  @override
  void onReadAckForGroupMessageUpdated() {}
}
```

When a voice message is received, the SDK automatically downloads the audio file.

For image and video messages, the SDK automatically generates a thumnail when you create the message. When an image or video message is received, the SDK automatically downloads the thumbnail. To manually download the attachment files, follow the steps:

1. Set `isAutoDownloadThumbnail` as true when initilizing the SDK.

  ```dart
  ChatOptions options = ChatOptions(
    appKey: appKey,
    isAutoDownloadThumbnail: false,
  );
  ```

2. Set the status callback for the download.

```dart
message.setMessageStatusCallBack(
  MessageStatusCallBack(
    onProgress: (progress) {},
    onSuccess: () {},
    onError: (e) {},
  ),
);
```

3. Download the thumbnail and get the path to it.

  ```dart
  ChatClient.getInstance.chatManager.downloadThumbnail(message);
  ```

  Get the path to the thumbnail from the `thumbnailLocalPath` member in the message body.

  ```dart
  // Get the message body of the image file.
  ChatImageMessageBody imgBody = message.body as ChatImageMessageBody;
  // Get the local path to the thumbnail.
  String? thumbnailLocalPath = imgBody.thumbnailLocalPath;
  ```

4. Call `downloadAttachment` to download the file.

  ```dart
  ChatClient.getInstance.chatManager.downloadAttachment(message);
  ```

  Get the path to the attachment from the `localPath` member in the message body.

  ```dart
  // Get the message body.
  ChatFileMessageBody fileBody = message.body as ChatFileMessageBody;
  // Get the local path of the file.
  String? localPath = fileBody.localPath;
  ```

### Recall a message

Two minutes after a user sends a message, this user can withdraw it. Contact support@agora.io if you want to adjust the time limit.

```dart
try {
  await ChatClient.getInstance.chatManager.recallMessage(msgId);
} on ChatError catch (e) {
```

You can also use `ChatManagerListener` to listen for the state of recalling the message:

```dart
// Occurs when the message is recalled.
void onMessagesRecalled(List<ChatMessage> messages) {}
```

## Next steps

After implementing sending and receiving messages, you can refer to the following documents to add more messaging functionalities to your app:

- [Manage local messages](./agora_chat_manage_message_flutter?platform=Flutter)
- [Retrieve conversations and messages from the server](./agora_chat_retrieve_message_flutter?platform=Flutter)
- [Message receipts](./agora_chat_message_receipt_flutter?platform=Flutter)


