After logging in to Agora Chat, users can send the following types of messages to a peer user, a chat group, or a chat room:
- Text messages, including hyperlinks and emojis.
- Attachment messages, including image, voice, video, and file messages.
- Location messages.
- CMD messages.
- Extended messages.
- Custom messages.

This page shows how to implement sending and receiving these messages using the Agora Chat SDK.

## Understand the tech

The process of sending and receiving a message is as follows:

1. The message sender creates a text, file, or attachment message using the corresponding `create` method.
2. The message sender calls `send` to send the message.
3. The message recipient calls `addEventHandler` to listen for message events and receive the message in the corresponding callback.

## Prerequistes

Before proceeding, ensure that you meet the following requirements:
- You have integrated the Agora Chat SDK, initialized the SDK and implemented the functionality of registering accounts and login. For details, see [Get Started with Agora Chat](./agora_chat_get_started_web?platform=Web).
- You understand the API call frequency limits as described in [Limitations](./agora_chat_limitation?platform=Web).

## Implementation

This section shows how to implement sending and receiving the various types of messages.

### Send a text message

Use the `Message` class to create a text message, and send the message. 

```javascript
// Send a text message.
function sendPrivateText() {

    let option = {
        // Set the message content.
        msg: "message content",
        // Set the username of the receiver.
        to: "username",
        // Set the chat type
        chatType: "singleChat",
    };
    // Create a text message.
    let msg = WebIM.message.create(opt);
    // Call send to send the message
    conn.send(msg).then(()=>{
        console.log("send private text Success");
    }).catch((e)=>{
        console.log("Send private text error");
    });
}
```

### Receive a message

You can use `addEventHandler` to listen for message events. You can add multiple events. When you no longer listen for an event, ensure that you remove the handler.

When a message arrives, the recipient recieves an `onXXXMessage` callback. Each callback contains one or more messages. You can traverse the message list, and parse and render these messages in this callback and render these messages.

```javascript
// Use `addEventHandler` to listen for callback events.
WebIM.conn.addEventHandler("eventName",{
    // Occurs when the app is connected.
    onOpened: function (message) {},
    // Occurs when the connection is lost.
    onClosed: function (message) {},
    // Occurs when the text message is received.
    onTextMessage: function (message) {},
    // Occurs when the emoji message is received.
    onEmojiMessage: function (message) {},
    // Occurs when the image message is received.
    onImageMessage: function (message) {},
    // Occurs when the CMD message is received.
    onCmdMessage: function (message) {},
    // Occurs when the audio message is received.
    onAudioMessage: function (message) {},
    // Occurs when the location message is received.
    onLocationMessage: function (message) {},
    // Occurs when the file message is received.
    onFileMessage: function (message) {},
    // Occurs when the custom message is received.
    onCustomMessage: function (message) {},
    // Occurs when the video message is received.
    onVideoMessage: function (message) {},
    // Occurs when the presence state is updated.
    onPresence: function (message) {}, 
    // Occurs when a contact invitation is received.
    onRoster: function (message) {},
    // Occurs when a group invitation is received.
    onInviteMessage: function (message) {},
    // Occurs when the local network is connected.
    onOnline: function () {},
    // Occurs when the local network is disconnected.
    onOffline: function () {},
    // Occurs when an error occurs.
    onError: function (message) {},
    // Occurs when the block list is updated, for example, if you add a contact to the block list. list contains all the usernames on the block list.
    onBlacklistUpdate: function (list) {
    },
    // Occurs when the message is recalled.
    onRecallMessage: function (message) {},
    // Occurs when the message is received.
    onReceivedMessage: function (message) {},
    // Occurs when the message delevery receipt is received.
    onDeliveredMessage: function (message) {},
    // Occurs when the message read receipt is received.
    onReadMessage: function (message) {},
    // Occurs when the local user is muted and still attempts to send a group message. This callback is triggered only on the local client, not on other clients in the group.
    onMutedMessage: function (message) {},
    // Occurs when the conversation read receipt is received.
    onChannelMessage: function (message) {},
});
```

### Recall a message

Two minutes after a user sends a message, this user can withdraw it. Contact support@agora.io if you want to adjust the time limit.

```javascript
/**
 * @param {Object} option.mid -  The message ID that you want to recall.
 * @param {Object} option.to -   The recipient of the message.
 * @param {Object} option.type - The message type: chat (one-to-one chat), group chat or chat room.
 */
let option = {
    mid: 'msgId',
    to: 'userID',
    chatType: 'singleChat'
};
connection.recallMessage(option).then((res) => {
    console.log('success', res)
}).catch((error) => {
    // Recalling the message fails, probably because the time limit for recalling the message is exceeded.
    console.log('fail', error)
```

You can also use `onRecallMessage` to listen for the message recall state:

```javascript
WebIM.conn.addEventHandler('MESSAGES',{
   onRecallMessage: => (msg) {
       // You can insert a message here, for example, XXX has recalled a message.
   	   console.log('Recalling the message succeeds'，msg) 
   }, 
})
```

### Send an attachment message

Voice, image, video, and file messages are essentially attachment messages. 

When sending an attachment message, the SDK takes the following steps:
1. Uploads the attachment to the server and gets the information of the attachment file on the server
2. Sends the attachment message, which contains the basic information of the message, and the path to the attachment file on the server.

For image and video messages, the SDK also automatically generates a thumbnail.

When receiving an attachment message, the SDK takes the following steps:
- For voice messages, the SDK automatically downloads the audio file.
- For image and video messages, the SDK automatically downloads the thumbnail of the image or video. To download the files, you need to call the `download` method.
- For file messages, you need to call the `download` method to download the file.


#### Send a voice message

Before sending a voice message, you should implement audio recording on the app level, which provides the URI and duration of the recorded audio file.

Refer to the following code example to create and send a voice message:

```javascript
  var sendPrivateAudio = function () {
      // Create a voice message.
      var msg = new WebIM.message('audio');
      // Select the local audio file.
      var input = document.getElementById('audio');
      // Turn the audio file to a binary file.
      var file = WebIM.utils.getFileUrl(input);
      var allowType = {
          'mp3': true,
          'amr': true,
          'wmv': true
      };
      if (file.filetype.toLowerCase() in allowType) {
          var option = {
              // Set the message type
              type: 'audio',
              file: file,
              // Set the length of the audio file in seconds.
              length: '3',
              // Set the username of the message receiver.
              to: 'username',
              // Set the chat type.
              chatType: 'singleChat',
              // Occurs when the audio file fails to be uploaded.
              onFileUploadError: function () {
                  console.log('onFileUploadError');
              },
              // Reports the progress of uploading the audio file.
              onFileUploadProgress: function (e) {
                  console.log(e)
              },
              // Occurs when the audio file is successfully uploaded.
              onFileUploadComplete: function () {
                  console.log('onFileUploadComplete');
              },
              ext: {file_length: file.data.size}
          };
          // Create a voice message.
          var msg = WebIM.message.create(option);
          // Call send to send the voice message.
          conn.send(msg).then((res)=>{
              // Occurs when the audio file is successfully sent.
              console.log('Success');
          }).catch((e)=>{
              // Occurs when the audio file fails to be sent 
              console.log("Fail");
          });
      }
  };
```

#### Send an image message

Refer to the following code example to create and send an image message:

```javascript
  var sendPrivateImg = function () {
      // Select the local image file.
      var input = document.getElementById("image");
      // Turn the image to a binary file.
      var file = WebIM.utils.getFileUrl(input);
      var allowType = {
          jpg: true,
          gif: true,
          png: true,
          bmp: true,
      };
      if (file.filetype.toLowerCase() in allowType) {
          var option = {
              // Set the message type.
              type: 'img',
              file: file,
              ext: {
                  // Set the image file length.
                  file_length: file.data.size,
              },
              // Set the username of the message receiver.
              to: "username",
              // Set the chat type.
              chatType: "singleChat",
              // Occurs when the image file fails to be uploaded.
              onFileUploadError: function () {
                  console.log("onFileUploadError");
              },
              // Reports the progress of uploading the image file.
              onFileUploadProgress: function (e) {
                  console.log(e);
              },
              // Occurs when the image file is successfully uploaded.
              onFileUploadComplete: function () {
                  console.log("onFileUploadComplete");
              },
          };
          // Create a voice message.
          var msg = WebIM.message.create(option);
          // Call send to send the voice message.
          conn.send(msg).then((res)=>{
              // Occurs when the audio file is successfully sent.
              console.log('Success');
          }).catch((e)=>{
              // Occurs when the audio file fails to be sent 
              console.log("Fail");
          });
      }
  };
```

#### Send a URL image message

To send a URL image message, make sure you set `useOwnUploadFun` as `true`.

```javascript
  var sendPrivateUrlImg = function () {

      var option = {
          chatType: 'singleChat',
          // Set the message type.
          type: "img",
          // Set the URL address of the image file.
          url: "img url",
          // Set the usernmae of the message receiver.
          to: "username",
      };
      // Create an image message.
      var msg = WebIM.message.create(option);
      // Call send to send to image file.
      conn.send(msg);
  };
```

#### Send a video message

Before sending a video message, you should implement video capturing on the app level, which provides the duration of the captured video file.

Refer to the following code example to create and send a video message:

```javascript
  var sendPrivateVideo = function () {

      // Select the local video file.
      var input = document.getElementById("video");
      // Turn the video to a binary file.
      var file = WebIM.utils.getFileUrl(input);
      var allowType = {
          mp4: true,
          wmv: true,
          avi: true,
          rmvb: true,
          mkv: true,
      };
      if (file.filetype.toLowerCase() in allowType) {
          var option = {
              // Set the message type
              type: 'video',
              file: file,
              // The username of the message receiver
              to: "username",
              // Set the chat type
              chatType: "singleChat",
              onFileUploadError: function () {
                  // Occurs when the file fails to be uploaded.
                  console.log("onFileUploadError");
              },
              onFileUploadProgress: function (e) {
                  // Reports the progress of uploading the file.
                  console.log(e);
              },
              onFileUploadComplete: function () {
                  // Occurs when the file is uploaded.
                  console.log("onFileUploadComplete");
              },
              ext: {file_length: file.data.size},
          };
          // Create a video message.
          var msg = WebIM.message.create(option);
          // Call send to send the video message.
          conn.send(msg).then((res)=>{
              // Occurs when the message is sent.
              console.log('success')
          }).catch((e)=>{
              // Occurs when the message fails to be sent, for example, because the local user is muted or blocked.
              console.log("Fail");
          });
      }
  };
```

#### Send a file message

Refer to the following code example to create, send, and receive a file message:

```javascript
  var sendPrivateFile = function () {
      // Select the local file.
      var input = document.getElementById("file");
      // Turn the file message to a binary file.
      var file = WebIM.utils.getFileUrl(input);
      var allowType = {
          jpg: true,
          gif: true,
          png: true,
          bmp: true,
          zip: true,
          txt: true,
          doc: true,
          pdf: true,
      };
      if (file.filetype.toLowerCase() in allowType) {
          var option = {
              // Set the message type.
              type: 'file',
              file: file,
              // Set the username of the message receiver.
              to: "username",
              // Set the chat type.
              chatType: "singleChat",
              // Occurs when the file fails to be uploaded.
              onFileUploadError: function () {
                  console.log("onFileUploadError");
              },
              // Reports the progress of uploading the file.
              onFileUploadProgress: function (e) {
                  console.log(e);
              },
              // Occurs when the file is uploaded.
              onFileUploadComplete: function () {
                  console.log("onFileUploadComplete");
              },
              ext: {file_length: file.data.size},
          };
          // Create a file message.
          var msg = WebIM.message.create(option);
          // Call send to send the file message.
          conn.send(msg).then((res) => {
              // Occurs when the file message is sent.
              console.log("Success");
          }).catch((e)=>{
              // Occurs when the file message fails to be sent.
              console.log("Fail");
          });
      }
  };
```

### Send a CMD message

CMD messages are command messages that instruct a specified user to take a certain action. The recipient deals with the command messages themselves.

The following code sample shows how to send and receive a CMD message:

```javascript
var options = {
  // Set the message type.
  type: 'cmd',  
  // Set the chat type.
  chatType: 'singleChat',
  // The username of the message receiver.
  to: 'username',
  // Set the custom action.
  action : 'action',
  // Set the extended message.
  ext :{'extmsg':'extends messages'}
}
// Create a CMD message.
var msg = WebIM.message.create(options);
// Call send to send the CMD message.
conn.send(msg).then((res)=>{
    // Occurs when the message is sent.
    console.log("Success")
}).catch((e)=>{
    // Occurs when the message fails to be sent.
    console.log("Fail");
});

```

### Send a customized message

Custom messages are self-defined key-value pairs that include the message type and the message content.

The following code example shows how to create and send a customized message:

```javascript
var sendCustomMsg = function () {

    // Set the custom event.
    var customEvent = "customEvent";
    // Set the custom message content with key-value pairs.
    var customExts = {};
    var options = {
        // Set the message type.
        type: "custom",
        // Set the username of the message receiver.
        to: "username",
        // Set the chat type.
        chatType: "singleChat",
        customEvent,
        customExts,
        // The extended field. Do not set it as null.
        ext: {},
    }
    // Create a custom message.
    var msg = WebIM.message.create(options);
    // Call send to send the custom message.
    conn.send(msg).then((res)=>{
        // Occurs when the message is sent.
        console.log("Success")
    }).catch((e)=>{
        // Occurs when the message fails to be sent.
        console.log("Fail");
    });
};
```

### Use message extensions

If the message types listed above do not meet your requirements, you can use message extensions to add attributes to the message. This can be applied in more complicated messaging scenarios.

```javascript
function sendPrivateText() {
    var options = {
        type: "txt",
        msg: "message content",
        to: "username",
        chatType: "singleChat",
        // Set the message extension.
        ext: {
            key1: "Self-defined value1",
            key2: {
                key3: "Self-defined value3",
            },
        }
    }
    let msg = WebIM.message.create(options);
    // Call send to send the extended message.
    conn.send(msg).then((res)=>{
        console.log("send private text Success");
    }).catch((e)=>{
        console.log("Send private text error");
    });
}
```

## Next steps

After implementing sending and receiving messages, you can refer to the following documents to add more messaging functionalities to your app:

- [Retrieve conversations and messages from the server](./agora_chat_retrieve_message_web?platform=Web)
- [Message receipts](./agora_chat_message_receipt_web?platform=Web)

