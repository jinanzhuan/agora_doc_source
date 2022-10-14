# Manage Chat Rooms

Chat rooms enable real-time messaging among multiple users.

Chat rooms do not have a strict membership, and members do not retain any permanent relationship with each other. Once a chat room member goes offline, this member does not receive any push messages from the chat room and automatically leaves the chat room after 5 minutes. Chat rooms are widely applied in live broadcast use cases such as stream chat in Twitch.

This page shows how to use the Agora Chat SDK to create and manage a chat room in your app.

## Understand the tech

The Agora Chat SDK provides the `Room`, `IRoomManager`, and `IRoomManagerDelegate` classes for chat room management, which allows you to implement the following features:

- Create and destroy a chat room
- Join and leave a chat room
- Retrieve the attributes of a chat room
- Retrieve the chat room list from the server
- Listen for chat room events

## Prerequisites

Before proceeding, ensure that you meet the following requirements:

- You have initialized the Agora Chat SDK. For details, see [Get Started with Unity](./agora_chat_get_started_unity).
- You understand the call frequency limit of the Agora Chat APIs supported by different pricing plans as described in [Limitations](./agora_chat_limitation).
- You understand the number of chat rooms supported by different pricing plans as described in [Pricing Plan Details](./agora_chat_plan).
- Only the app super admin has the privilege of creating a chat room. Ensure that you have added an app super admin by [calling the super-admin RESTful API](./agora_chat_restful_chatroom_superadmin#adding-a-chat-room-super-admin).

## Implementation

This section describes how to call the APIs provided by the Agora Chat SDK to implement chat room features.

### Create a chat room

Only the [app super admin](./agora_chat_restful_chatroom_superadmin) can call `CreateRoom` to create a chat room and set the chat room attributes such as the chat room name, description, and maximum number of members. Once a chat room is created, the super admin automatically becomes the chat room owner.

The following code sample shows how to create a chat room:

```c#
SDKClient.Instance.RoomManager.CreateRoom(subject, description, welcomeMsg, maxUserCount, members,handle: new ValueCallBack<Room>(
  onSuccess: (room) => {
  },
  onError:(code, desc) => {
  }
));
```

### Destroy a chat room

Only the chat room owner can call `DestroyRoom` to disband a chat room. Once a chat room is disbanded, all chat room members receive the `OnDestroyedFromRoom` callback and are immediately removed from the chat room.

The following code sample shows how to destroy a chat room:

```c#
SDKClient.Instance.RoomManager.DestroyRoom(roomId, new CallBack(
  onSuccess: () => {
  },
  onError: (code, desc) => {
  }
));
```

### Join a chat room

Refer to the following steps to join a chat room:

1. Call `FetchPublicRoomsFromServer` to retrieve the list of chat rooms from the server and locate the ID of the chat room that you want to join.

2. Call `JoinRoom` to pass in the chat room ID and join the specified chat room. Once a user joins a chat room, all the other chat room members receive the `OnMemberJoinedFromRoom` callback.

The following code sample shows how to join a chat room:

```c#
// Retrieve the list of chat rooms from the server
SDKClient.Instance.RoomManager.FetchPublicRoomsFromServer(handle: new ValueCallBack<PageResult<Room>>(
            // `result` is of PageResult<Room> type
            onSuccess: (result) => {
            },
            onError:(code, desc) => {
            }
        ));

// Join a chat room
SDKClient.Instance.RoomManager.JoinRoom(roomId, new ValueCallBack<Room>(
  onSuccess: (room) => {
  },
  onError: (code, desc) => {
  }
));
```

### Leave a chat room

All chat room members can call `LeaveRoom` to leave the specified chat room. Once a member leaves the chat room, all the other chat room members receive the `OnMemberExitedFromRoom` callback.

**Note**: Unlike chat group owners (who cannot leave their groups), a chat room owner can leave a chat room. After re-entering the chat room, this user remains the chat room owner.

The following code sample shows how to leave a chat room:

```c#
SDKClient.Instance.RoomManager.LeaveRoom(roomId, new CallBack(
  onSuccess: () => {
  },
  onError: (code, desc) => {
  }
));
```

By default, after a user leaves a chat room, the Agora Chat SDK removes all chat room messages on the local device. If you do not want these messages removed, set `Options#DeleteMessagesAsExitRoom` to `false` when initializing the SDK.

The following code sample shows how to retain the chat room messages after leaving a chat room:

```c#
Options options = new Options();
options. DeleteMessagesAsExitRoom = false;
```

### Retrieve the chat room attributes

All chat room members can call `FetchRoomInfoFromServer` to retrieve the attributes of the a chat room, including the chat room ID, name, description, announcements, owner, admin list, member list, block list, mute list, maximum number of members, and whether all members are muted.

The following code sample shows how to retrieve the chat room attributes:

```c#
SDKClient.Instance.RoomManager.FetchRoomInfoFromServer(roomId, new ValueCallBack<Room>(
  onSuccess: (room) => {
  },
  onError: (code, desc) => {
  }
));
```

### Retrieve the chat room list from the server

Users can call `FetchPublicRoomsFromServer` to get the chat room list from the server. You can get a maximum of 1,000 chat rooms at each call.

```c#
// You can set the value of `pageSize` to a maximum of 1000.
SDKClient.Instance.RoomManager.FetchPublicRoomsFromServer(pageNum, pageSize, handle: new ValueCallBack<PageResult<Room>>(
  // `rooms` is of PageResult<Room> type.
  onSuccess: (rooms) => {
  },
  onError:(code, desc) => {
  }
));
```

### Listen for chat room events

To monitor the chat room events, you can listen for the callbacks in the `IRoomManagerDelegate` class and add app logics accordingly. If you want to stop listening for the callback, make sure that you remove the listener to prevent memory leakage.

The following code sample shows how to add and remove the chat room listener:

```c#
// Inherits and implements the IRoomManagerDelegate class.
public class RoomManagerDelegate : IRoomManagerDelegate {
    ......
    public void OnDestroyedFromRoom(string roomId, string roomName)
    {
    }
    ......
}
// Adds the chat room listener.
RoomManagerDelegate adelegate = new RoomManagerDelegate();
SDKClient.Instance.RoomManager.AddRoomManagerDelegate(adelegate);

// Removes the chat room listener.
SDKClient.Instance.RoomManager.AddRoomManagerDelegate(adelegate);
```

Refer to the following code sample to listen for chat room events:

```c# 
public interface IRoomManagerDelegate
    {
        /**
        * Occurs when a chat room instance is destroyed.
        * 
        * @param roomId        The chat room ID
        * @param roomName      The chat room name
        *
        */
        void OnDestroyedFromRoom(string roomId, string roomName);
        /**
        * Occurs when a user joins a chat room.
        * 
        * @param roomId        The chat room ID
        * @param participant   The user ID of the new chat room member
        *
        */
        void OnMemberJoinedFromRoom(string roomId, string participant);
        /**
        * Occurs when a member leaves a chat room.
        * 
        * @param roomId        The chat room ID
        * @param roomName      The chat room name
        * @param participant   The user ID of the member who leaves the chat room
        *
        */
        void OnMemberExitedFromRoom(string roomId, string roomName, string participant);
        /**
        * Occurs when a member is removed from a chat room.
        *
        * @param roomId        The chat room ID
        * @param roomName      The chat room name
        * @param participant   The user ID of the member who is removed from the chat room
        *
        */
        void OnRemovedFromRoom(string roomId, string roomName, string participant);
        /**
        * Occurs when a member is added to the chat room mute list.
        *
        * @param roomId        The chat room ID
        * @param mutes         The user IDs of the members added to the chat room mute list
        * @param expireTime    The Unix timestamp when the mute duration expires, in milliseconds
        *
        */
        void OnMuteListAddedFromRoom(string roomId, List<string> mutes, long expireTime);
        /**
        * Occurs when a member is removed from the chat room mute list.
        *
        * @param roomId        The chat room ID
        * @param mutes         The user IDs of the members removed from the chat room mute list
        *
        */
        void OnMuteListRemovedFromRoom(string roomId, List<string> mutes);
        /**
        * Occurs when a member is promoted to a chat room admin.
        *
        * @param roomId        The chat room ID
        * @param admin         The user ID of the member promoted to an admin
        *
        */
        void OnAdminAddedFromRoom(string roomId, string admin);
        /**
        * Occurs when an admin is demoted to a chat room member.
        *
        * @param  roomId       The chat room ID
        * @param  admin        The user ID of the admin demoted to a member
        *
        */
        void OnAdminRemovedFromRoom(string roomId, string admin);
        /**
        * Occurs when the chat room ownership is transferred.
        *
        * @param roomId        The chat room ID
        * @param newOwner      The user ID of the new chat room owner
        * @param oldOwner      The user ID of the former chat room owner
        *
        */
        void OnOwnerChangedFromRoom(string roomId, string newOwner, string oldOwner);
        /**
        * Occurs when the chat room announcement is updated.
        * @param roomId        The chat room ID
        * @param announcement  The updated chat room announcement.
        *
        */
        void OnAnnouncementChangedFromRoom(string roomId, string announcement);
        /**
         * Occurs when the custom chat room attributes (key-value maps) are updated.
         *
         * @param roomId        The chat room ID
         * @param kv            The updated attributes
         * @param from          The user ID of the operator
         */
        void OnChatroomAttributesChanged(string roomId, Dictionary<string, string> kv, string from);
         /**
         * Occurs when the custom chat room attributes (key-value maps) are removed.
         *
         * @param roomId        The chat room ID.
         * @param keys          The keys of removed attributes
         * @param from          The user ID of the operator
         */
        void OnChatroomAttributesRemoved(string roomId, List<string> keys, string from);
    }
```