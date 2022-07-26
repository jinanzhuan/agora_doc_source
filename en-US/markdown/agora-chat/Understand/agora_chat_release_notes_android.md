This page provides release notes for the Agora Chat Android SDK.

## v1.0.6

v1.0.6 was released on Jul 22, 2022.

#### New features

- Supports marking whether a message is an online message by using the `isOnlineState` member in `ChatMessage`.
- Adds an error code 509 `MESSAGE_CURRENT_LIMITING` in `Error`, which means that the chat group message has exceeded the concurrent limit.
- Adds an `onSpecificationChanged` callback in `GroupChangeListener`, which occurs when the state specification updates.
- Adds a `bindDeviceToken` method in `PushManager`, which binds the device token.

#### Improvements

- Improved thread-related methods and classes. Compared with 1.0.4, this release used `ChatThread` to replace `ChatThreadInfo`.
- Assigned a value to `groupName` in the `onInvitationReceived` callback.
- Removed the CBC and EBC encryption algorithm in the Android layer.
- Upgraded the network link library.
- Supported sending messages with a remote address as the attachment.

#### Issues fixed

- The retrieved reaction object was empty.
- Devices running earlier Android versions failed to load the database.


## v1.0.4

v1.0.4 was released on May 17, 2022.

#### New features

- Supports reaction, which enables users to add reaction emojis to the specified message.
- Supports content moderation with the reportMessage method.
- Supports message push configuration, which enables users to configure various push settings.

#### Improvements

- Enhanced DNS configuration for retrieving the server access point.
- Improved data reports.
- Changed the file name of libsqlcipher to avoid conflict when using the official AAR.
- Added support for double and float data types for the ext attribute in ChatMessage.
- Changed openssl to boringssl.
- Changed the minimum API level to 21 (Android 5.0).

#### Issues fixed

- Issues reported when uploading the app to Google Play caused by encryption algorithm.
- The translation API did not take effect.


## v1.0.3.1

v1.0.3.1 was released on April 27, 2022. This release fixed the occasional issue of not being to display the retrieved historical messages.

## v1.0.3

v1.0.3 was released on April 19, 2022.

#### New features

Supports the presence feature, which indicates the online status of the user.

#### Improvements

- Shortened the time out for sending messages.
- Enhanced the request success rate.
- Supported the upgraded OPPO push (from 2.1.0 to 3.0.0) and VIVO push (from 2.3.1 to 3.is 0.0.4_484).

#### Issues fixed

Fixed PendingIntent, which caused warnings when uploading apps to Google Play.

## v1.0.2

v1.0.2 was released on Feb 22, 2022.

#### New features

- Supports deleting conversations on the server by calling deleteConversationFromServer.
- Supports customizing messages using extension fields, badges, CMD messages for message push.
- Adds an error code 221 `USER_NOT_ON_ROSTER` which is reported when the user sends a message to another user that is not a contact.
- Supports recalling messages using the RESTful API.

#### Improvements

Reduced the time for preparing to send messages under poor network conditions.

#### Issues fixed

- The message re-sending was interrupted by the connection success event.
- Memory leak.
- Crashes caused by incorrect time calculation.

## v1.0.1.1

v1.0.1.1 was released on December 30, 2021.

This release fixed an issue where the database failed to load under extreme conditions.

## v1.0.1

v1.0.1 was released on December 27, 2021.

#### New features

v1.0.1 adds the following features:

- Supports setting the building name when creating a location message.
- Supports deleting local messages before a specific time.
- Supports getting the count of messages in one conversation.

#### Fixed issues

This release fixed the following issues:

- Some crash issues occurred.
- An issue occurred in the database encryption.

#### API changes

v1.0.1 adds the following APIs:

- `createLocationSendMessage` [1/2]
- `deleteMessagesBeforeTimestamp`
- `getAllMsgCount`

## v1.0.0

v1.0.0 was released on December 6, 2021.

<div class="alert warning">This release has an issue that the database occasionally fails to load. Agora recommends you upgrade to the latest version as soon as possible.</div>

#### New features

This release supports getting the users' login status through the `isLoggedIn` and `isLoggedInBefore` methods.

#### Improvements

This release makes the following improvements:

- Optimizes the logic of renewing push tokens, reducing server request times.
- Improves the login speed.
- Uses only HTTPS for REST operations by default.
- Optimizes the logic of token expiration.

#### Fixed issues

This release fixed the following issues:

- The fetched history messages were incomplete.
- Crashes occurred in certain scenarios.
- An issue occurred in displaying the unread status of messages.