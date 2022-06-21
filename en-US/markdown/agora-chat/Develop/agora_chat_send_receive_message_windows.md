After logging in to Agora Chat, users can send the following types of messages to a peer user, a chat group, or a chat room:
- Text messages, including hyperlinks and emojis.
- Attachment messages, including image, voice, video, and file messages.
- Location messages.
- CMD messages.
- Extended messages.
- Custom messages.

This page shows how to implement sending and receiving these messages using the Agora Chat SDK.

## Understand the tech

The Agora Chat SDK uses the `IChatMessage` and `Message` classes to send, receive, and withdraw messages.

The process of sending and receiving a message is as follows:

1. The message sender creates a text, file, or attachment message using the corresponding `Create` method.
2. The message sender calls `SendMessage` to send the message.
3. The message recipient calls `AddChatManagerDelegate` to listens for message events and receives the message in the `OnMessageReceived` callback.

## Prerequistes

Before proceeding, ensure that you meet the following requirements:
- You have integrated the Agora Chat SDK, initialized the SDK and implemented the functionality of registering accounts and login. For details, see [Get Started with Agora Chat](./agora_chat_get_started_windows?platform=Windows).
- You understand the API call frequency limits as described in [Limitations](./agora_chat_limitation?platform=Windows).

## Implementation

This section shows how to implement sending and receiving the various types of messages.

### Send a text message

Use the `Message` class to create a text message, and `IChannelManager` to send the message. 

```C#
// Call CreateTextSendMessage to create a text message. Set `content` as the content of the text message, and `toChatUsernames` the user ID of the message recipient.
Message msg = Message.CreateTextSendMessage(toChatUsername, content);
// Set the message type using the `MessageType` attribute in `Message`.
// You can set `MessageType` as `Chat`, `Group`, or `Room`, which indicates whether to send the message to a peer user, a chat group, or a chat room.  
msg.MessageType = MessageType.Group;
// Call SendMessage to send the message.
// When sending the message, you can instantiate a `Callback` object to listen for the result of the message sending. You can also update the message state in this callback, for example, by adding a pop-up box that reminds the message sending fails.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Message sending succeeds.");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
  }            
));
```

### Receive a message

You can use `IChatManagerDelegate` to listen for message events. You can add multiple `IChatManagerDelegates` to listen for multiple events. When you no longer listen for an event, ensure that you remove the delegate.

When a message arrives, the recipient recieves an `OnMessgesReceived` callback. Each callback contains one or more messages. You can traverse the message list, and parse and render these messages in this callback and render these messages.

```C#
// Inherit and instantiate IChatManagerDelegate.
public class ChatManagerDelegate : IChatManagerDelegate {
    // Listen for OnMessagesReceived.
    public void OnMessagesReceived(List<Message> messages)
    {
      // Traverse the message list, and parse and render the messages.
    }
}
// Add the delegate to listen for message callback.
ChatManagerDelegate adelegate = new ChatManagerDelegate();
SDKClient.Instance.ChatManager.AddChatManagerDelegate(adelegate);
// Remove the delegate.
SDKClient.Instance.ChatManager.RemoveChatManagerDelegate(adelegate);
```

### Recall a message

Two minutes after a user sends a message, this user can withdraw it. Contact sales@agora.io if you want to adjust the time limit.

```C#
// Call `RecallMessage` to recall the message.
SDKClient.Instance.ChatManager.RecallMessage("Message ID", new CallBack(
  onSuccess: () => {
    Debug.Log("Message recall succeeds.");
  },
  onProgress: (progress) => {
    Debug.Log($"Message recall progress {progress}");
  },
  onError: (code, desc) => {
    Debug.Log($"Message recall fails, errCode={code}, errDesc={desc}");
  }
 ));
```

You can also use `IChatManagerDelegate` to listen for the message withdraw state:

```C#
// The SDK triggers OnMessageRecalled when the message is withdrawn.
void OnMessagesRecalled(List<Message> messages);
```

### Send and receive an attachment message

Voice, image, video, and file messages are issentially attachment messages. This section introduces how to send these types of messages.

#### Send and receive a voice message

Before sending a voice message, you should implement audio recording on the app level, which provides the URI and duration of the recorded audio file.

Refer to the following code example to create and send a voice message:

```C#
// Call CreateVoiceSendMessage to create a voice message.
// Set `localPath` as the path of the audio file on the local device, `displayName` as the display name of the message, `fileSize` as the size of the audio file, and `duration` as the duration (in seconds) of the audio file. For audio files, you can set `displayName` as "".
Message msg = Message.CreateVoiceSendMessage(toChatUsername, localPath, displayName, fileSize, duration);
// Set the message type using the `MessageType` attribute in `Message`.
// You can set `MessageType` as `Chat`, `Group`, or `Room`, which indicates whether to send the message to a peer user, a chat group, or a chat room. 
msg.MessageType = MessageType.Group;
// Call SendMessage to send the message.
// When sending the message, you can instantiate a `Callback` object to listen for the result of the message sending. You can also update the message state in this callback, for example, by adding a pop-up box that reminds the message sending fails.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Message sending succeeds.");
  },
  onProgress: (progress) => {
    Debug.Log($"Message sending progress {progress}");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId}Message sending fails, errCode={code}, errDesc={desc}");
  }            
));
```

When the recipient receives the message, refer to the following code example to get the audio file:

```C#
// "Message ID" is the ID of the message returned in the onSuccess callback after the SDK successfully sends the message.
Message msg = SDKClient.Instance.ChatManager.LoadMessage("Message ID");
if (msg != null)
{
  ChatSDK.MessageBody.VoiceBody vb = (ChatSDK.MessageBody.VoiceBody)msg.Body;
  // RemotePath indicates the path of the audio file on the server.
  string voiceRemoteUrl = vb.RemotePath;
  // LocalPath indicates the path of the audio file on the local device.
  string voiceLocalUri = vb.LocalPath;
}
else {
  Debug.Log($"Fails to find the message");
}
```

#### Send and receive an image message

By default, the SDK compresses the image file before sending it. To send the originial file, you can set `original` as `true`.

Refer to the following code example to create and send an image message:

```C#
// Create SendMessage to send the image message.
// Set `localPath` as the path of the image file on the local device, `displayName` as the display name of the message, `fileSize` as the size of the image file, `width` as the width (in pixels) of the thumbnail, and `height` as the height (in pixels) of the thumbnail. 
// `orignial` indicates whether to send the original image file. The default value is `false`. By default, the SDK compresses image files the exceeds 100 KB and sends the thumbnail. To send the originial image, set this parameter as `true`.
Message msg = Message.CreateImageSendMessage(toChatUsername,localPath, displayName, fileSize, original, width , height);
// Set the message type using the `MessageType` attribute in `Message`.
// You can set `MessageType` as `Chat`, `Group`, or `Room`, which indicates whether to send the message to a peer user, a chat group, or a chat room. 
msg.MessageType = MessageType.Group;
// Call SendMessage to send the message.
// When sending the message, you can instantiate a `Callback` object to listen for the result of the message sending. You can also update the message state in this callback, for example, by adding a pop-up box that reminds the message sending fails.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Message sends succeeds.");
  },
  onProgress: (progress) => {
    Debug.Log($"Message sending progress {progress}");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
  }            
));
```

When the recipient receives the message, refer to the following code example to get the thumbnail and attachment file of the image message:

```C#
// "Message ID" is the ID of the message returned in the onSuccess callback after the SDK successfully sends the message.
Message msg = SDKClient.Instance.ChatManager.LoadMessage("Message ID");
if (msg != null)
{
  ChatSDK.MessageBody.ImageBody ib = (ChatSDK.MessageBody.ImageBody)msg.Body;
  // RemotePath indicates the path of the image file on the server.
  string imgRemoteUrl = ib.RemotePath;
  // ThumbnaiRemotePath indicates the path of the thumbnail on the server.
  string thumbnailUrl = ib.ThumbnaiRemotePath;
  // LocalPath indicates the path of the image file on the local device.
  string imgLocalUri = ib.LocalPath;
  // ThumbnailLocalPath indicates the path of the thumbnail on the local device.
  Uri thumbnailLocalUri = ib.ThumbnailLocalPath;
}
else {
  Debug.Log($"Fails to find the message.");
}
```

<div class="alert note">If `Options.IsAutoDownload` is set as `true` on the recipient's client, the SDK automatically downloads the thumbnail after receiving the message. If not, you need to call `SDKClient.Instance.ChatManager.DownloadThumbnail` to download the thumbnail and get the path from the `ThumbnailLocalPath` member in `msg.Body`.</div>

#### Send and receive a video message

Before sending a video message, you should implement video capturing on the app level, which provides the duration of the captured video file.

Refer to the following code example to create and send a video message:

```C#
Message msg = Message.CreateVideoSendMessage(toChatUsername, localPath, displayName, thumbnailLocalPath, fileSize, duration, width, height);
// Call SendMessage to send the message.
// When sending the message, you can instantiate a `Callback` object to listen for the result of the message sending. You can also update the message state in this callback, for example, by adding a pop-up box that reminds the message sending fails.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Messag sending succeeds");
  },
  onProgress: (progress) => {
    Debug.Log($"Message sending progress {progress}");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
  }            
));
```

By default, when the recipient receives the message, the SDK downloads the thumbnail of the video message. 

If you do not want the SDK to automatically download the video thumbnail, set `Options.IsAutoDownload` as `false`, and to download the thumbnail, you need to call `SDKClient.Instance.ChatManager.DownloadThumbnail`, and get the path of the thumbnail from the `ThumbnailLocalPath` member in `msg.Body`.

To download the actual video file, call `SDKClient.Instance.ChatManager.DownloadAttachment`, and get the path of the video file from the `LocalPath` member in `msg.Body`.

```C#
// When the recipent receives a video message, the SDK downloads and then open the video file.
SDKClient.Instance.ChatManager.DownloadAttachment("Message ID", new CallBack(
  onSuccess: () => {
    Debug.Log($"Attachment download succeeds.");
    Message msg = SDKClient.Instance.ChatManager.LoadMessage("Message ID");
    if (msg != null)
    {
      if (msg.Body.Type == ChatSDK.MessageBodyType.VIDEO) {
        ChatSDK.MessageBody.VideoBody vb = (ChatSDK.MessageBody.VideoBody)msg.Body;
        // LocalPath indicates the path of the video file on the local device.
        string videoLocalUri = vb.LocalPath;
        // You add open the video file after getting the path.
      }
    }
    else {
      Debug.Log($"Fails to find the message.");
    }
  },
  onProgress: (progress) => {
    Debug.Log($"Attachment download progress {progress}");
  },
  onError: (code, desc) => {
    Debug.Log($"Attachment download fails, errCode={code}, errDesc={desc}");
  }
));
```

#### Send and receive a file message

Refer to the following code example to create, send, and receive a file message:

```C#
// Call CreateFileSendMessage to create a file message.
// Set `localPath` as the path of the file on the local device, `displayName` as the display name of the file message, and `fileSize` as the size of the file.
Message msg = Message.CreateFileSendMessage(toChatUsername,localPath, displayName, fileSize);
// Set the message type using the `MessageType` attribute in `Message`.
// You can set `MessageType` as `Chat`, `Group`, or `Room`, which indicates whether to send the message to a peer user, a chat group, or a chat room. 
msg.MessageType = MessageType.Group;
// Call SendMessage to send the message.
// When sending the message, you can instantiate a `Callback` object to listen for the result of the message sending. You can also update the message state in this callback, for example, by adding a pop-up box that reminds the message sending fails.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Message sending succeeds.");
  },
  onProgress: (progress) => {
    Debug.Log($"Message sending progress {progress}");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
  }            
));

// When the recipient receives the message, call `LoadMessage` to get the attachment file.
// "Message ID" is the ID of the message returned in the onSuccess callback after the SDK successfully sends the message.
Message msg = SDKClient.Instance.ChatManager.LoadMessage("Message ID");
if (msg != null)
{
  ChatSDK.MessageBody.FileBody fb = (ChatSDK.MessageBody.FileBody)msg.Body;
  // RemotePath indicates the path of the file on the server.
  string fileRemoteUrl = fb.RemotePath;
  // LocalPath indicates the path of the file on the local device.
  string fileLocalUri = fb.LocalPath;
}
else {
  Debug.Log($"Fails to find the message.");
}
```

### Send a location message

To send and receive a location message, you need to integrate a third-party map service provider. When sending a location message, you get the longitude and latitude information of the location from the map service provider; when receiving a location message, you extract the received longitude and latitude information and displays the location on the third-party map.

```C#
// Call CreateLocationSendMessage to create a location message.
// Set `locationAddress` as the address of the location and `buildingName` as the the name of the building.
Message msg = Message.CreateLocationSendMessage(toChatUsername, latitude, longitude, locationAddress, buildingName);
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId} Message sending succeeds.");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
  }   
));
```

### Send and receive a CMD message

CMD messages are command messages that instruct a specified user to take a certain action. The recipient deals with the command messages themselves.

<div class="alert note"><ul><li>CMD messages are not stored in the local database.</li><li>Actions beginning with `em_` and `easemob::` are internal fields. Do not use them.</li></ul></div>

```C#
// Use `action` to customize the message
string action = "actionXXX";
Message msg = Message.CreateCmdSendMessage(toChatUsername, action);
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
   onSuccess: () => {
      Debug.Log($"{msg.MsgId} Message sending succeeds.");
   },
   onError: (code, desc) => {
      Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
   }
));
```

To notify the recipient that a CMD message is received, use a seperate delegate so that users can deal with the message differently.

```C#
// Inherit and instantiate `IChatManagerDelegate`.
public class ChatManagerDelegate : IChatManagerDelegate {
    // Occurs when the message is received.
    public void OnMessagesReceived(List<Message> messages)
    {
      // Traverse, parse, and display the message.
    }
    // Occurs when a CMD message is received.
    void OnCmdMessagesReceived(List<Message> messages)
    {
      // Traverse, parse, and display the message.
    }
}
// Call AddChatManagerDelegate to add a message delegate.
ChatManagerDelegate adelegate = new ChatManagerDelegate()
SDKClient.Instance.ChatManager.AddChatManagerDelegate(adelegate);
```

### Send a customized message

Custom messages are self-defined key-value pairs that include the message type and the message content.

The following code example shows how to create and send a customized message:

```C#
// Set `event` as the customized message type, for example, gift.
string event = "gift";
Dictionary<string, string> adict = new Dictionary<string, string>();
adict.Add("key", "value");
// Call CreateCustomSendMessage to create a customized message.
Message msg = Message.CreateCustomSendMessage(toChatUsername, event, adict);
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
   onSuccess: () => {
      Debug.Log($"{msg.MsgId} Message sending succeeds.");
   },
   onError: (code, desc) => {
      Debug.Log($"{msg.MsgId} Message sending fails, errCode={code}, errDesc={desc}");
   }
));
```

### Use message extensions

If the message types listed above do not meet your requirements, you can use message extensions to add attributes to the message. This can be applied in more complicated messaging scenarios.

```C#
Message msg = Message.CreateTextSendMessage(toChatUsername, content);
// Add message attributes.
AttributeValue attr1 = AttributeValue.Of("value", AttributeValueType.STRING);
AttributeValue attr2 = AttributeValue.Of(true, AttributeValueType.BOOL);
msg.Attributes.Add("attribute1", attr1);
msg.Attributes.Add("attribute2", attr2);
// Send the message.
SDKClient.Instance.ChatManager.SendMessage(ref msg, new CallBack(
  onSuccess: () => {
    Debug.Log($"{msg.MsgId}发送成功");
  },
  onError:(code, desc) => {
    Debug.Log($"{msg.MsgId}发送失败，errCode={code}, errDesc={desc}");
  }            
));

// When the recipient receives the message, get the extension attributes.
bool found = false;
string str = msg.GetAttributeValue<string>(msg.Attributes, "attribute1", found);
if (found) {
  // Use variable str.
}
found = false；
bool b = msg.GetAttributeValue<bool>(msg.Attributes, "attribute2", found);
if (found) {
  // Use variable b.
}
```

## Next steps

After implementing sending and receiving messages, you can refer to the following documents to add more messaging functionalities to your app:

- [Manage local messages](./agora_chat_manage_message_windows?platform=Windows)
- [Retrieve conversations and messages from the server](./agora_chat_retrieve_message_windows?platform=Windows)
- [Message receipts](./agora_chat_message_receipt_windows?platform=Windows)