# telegram-bot-lua

A feature-filled Telegram bot API library written in Lua, created by [Matt](https://t.me/wrxck). This library includes functions that allow you to use any Telegram bot API method. It is frequently updated and also includes extensive tools to reduce the amount of code needed to perform more elaborate and complex actions.

This library was written to complement the "[mattata](https://github.com/wrxck/mattata)" project, allowing anybody to create their own Telegram bot with little coding knowledge needed (that is, except a basic knowledge of Lua and the inspiration provided by [@mattatabot](https://t.me/mattatabot) and this library).

## Installation

To install this library, make sure you have Lua 5.3 and LuaRocks installed, then run `luarocks install telegram-bot-lua`.
That's it! Now you just need to include it in your bot's source code, with the configure function passing your bot auth token (the one you received from the [BotFather](https://t.me/BotFather)), like this:

```Lua
local api = require('telegram-bot-lua.core').configure('bot123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11')
```

Now you're ready to create your own bot!

## Update Handling

The Telegram bot API returns updates in the form of a JSON-encoded object. This object has several different types of updates, and there is a function for each one - they are all optional, but if you didn't use any of them then your bot wouldn't do anything.

To handle messages (a.k.a. the `update.message` object), you need to use the `api.on_message(message)` function. The example shown below would iterate over every `update.message` object, and reply to messages that contain the text `ping` with the word `pong`:

```Lua
function api.on_message(message)
    if message.text and string.match(message.text, 'ping') then
        api.send_message(message.chat.id, 'pong')
    end
end
```

You'll notice I've used another function from the telegram-bot-lua library we included as `api`. There is a function for every method in the [Telegram bot API](https://core.telegram.org/bots/api). Scroll down to see examples of method-binded functions.

To handle channel posts (a.k.a. the `update.channel_post` object), you need to use the `api.on_channel_post(channel_post)` function. The example shown below would iterate over every `update.channel_post` object, and reply to channel posts that contain the text `ping` with the word `pong`:

```Lua
function api.on_channel_post(channel_post)
    if channel_post.text and string.match(channel_post.text, 'ping') then
        api.send_message(channel_post.chat.id, 'pong')
    end
end
```

The reason channel posts are handled in a different function to messages is because messages always contain an object containing information about the sender (a.k.a. `update.message.from`), whereas channel posts only hold this information if the `Sign Messages` option is enabled in the channel settings.

To handle edited messages (a.k.a. the `update.edited_message` object), you need to use the `api.on_edited_message(edited_message)` function. The example shown below would iterate over every `update.edited_message` object and reply to messages that have been edited to contain the text `ping` with the word `pong`:

```Lua
function api.on_edited_message(edited_message)
    if edited_message.text and string.match(edited_message.text, 'ping') then
        api.send_message(edited_message.chat.id, 'pong')
    end
end
```

To handle edited channel posts (a.k.a. the `update.edited_channel_post` object), you need to use the `api.on_edited_channel_post(edited_channel_post)` function. The example shown below would iterate over every `update.edited_channel_post` object and reply to channel posts that have been edited to contain the text `ping` with the word `pong`:

```Lua
function api.on_edited_channel_post(edited_channel_post)
    if edited_channel_post.text and string.match(edited_channel_post.text, 'ping') then
        api.send_message(edited_channel_post.chat.id, 'pong')
    end
end
```

To handle inline queries (a.k.a. the `update.inline_query` object), you need to use the `api.on_inline_query(inline_query)` function. The example shown below would iterate over every `update.inline_query` object, and respond with an inline article:

```Lua
function api.on_inline_query(inline_query)
    api.answer_inline_query(
        inline_query.id,
        api.inline_result():type('article'):id(1):title('Title'):description('Description'):input_message_content(
            api.input_text_message_content('Message')
        )
    )
end
```

The `api.inline_result()` function is one of the many extensive tools this API offers - this particular one allows you to build inline results using Lua methods through metatables.

To handle chosen inline results (a.k.a. the `update.chosen_inline_result` object), you need to use the `api.on_chosen_inline_result(chosen_inline_result)` function. The example shown below would iterate over every `update.chosen_inline_result` object, and print the JSON-encoded object to the terminal, using the `dkjson` library:

```Lua
function api.on_chosen_inline_result(chosen_inline_result)
    print(
        require('dkjson').encode(chosen_inline_result)
    )
end
```

To handle callback queries (a.k.a. the `update.callback_query` object), you need to use the `api.on_callback_query(callback_query)` function. The example shown below would iterate over every `update.callback_query` object, and send an alert  to the person who pressed the inline keyboard button, containing their numerical ID:

```Lua
function api.on_callback_query(callback_query)
    api.answer_callback_query(
        callback_query.id,
        callback_query.from.id
    )
)
```

Although it's recommended to do so, you're not limited to handling specific update types through these functions – to do something with the original `update` object, use the function `api.on_update(update)`. The example shown below would iterate over every `update` object, and print the update type to the terminal:

```Lua
function api.on_update(update)
    if update.message then print('message')
    elseif update.edited_message then print('edited message')
    elseif update.channel_post then print('channel post')
    elseif update.edited_channel_post then print('edited channel post')
    elseif update.inline_query then print('inline query')
    elseif update.callback_query then print('callback query')
    elseif update.chosen_inline_result then print('chosen inline result')
    else print('unknown update type')
end
```

After these functions, you must use:

```Lua
api.run()
```

This is what will run your bot!

## API Methods

#### getMe

A simple function for testing your bot's auth token, using Telegram's `getMe` method. Requires no parameters.

```Lua
api.get_me()
```

#### sendMessage

Use this function to send text messages, using Telegram's `sendMessage` method.

```Lua
api.send_message(
    chat_id,
    text,
    parse_mode,
    disable_web_page_preview,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters                  | Type                                                                             | Required | Description                                                                                                                                                                    |
|-----------------------------|----------------------------------------------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id                    | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                       |
| text                        | String                                                                           | Yes      | Text of the message to be sent                                                                                                                                                 |
| parse\_mode                 | String                                                                           | Optional | Send `Markdown` or `HTML`, if you want Telegram apps to show bold, italic, fixed-width text or inline URLs in your bot's message.                                              |
| disable\_web\_page\_preview | Boolean                                                                          | Optional | Disables link previews for links in this message                                                                                                                               |
| disable\_notification       | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                |
| reply\_to\_message\_id      | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                          |
| reply\_markup               | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user. |

#### forwardMessage

Use this function to forward messages of any kind, using Telegram's `forwardMessage` method.

```Lua
api.forward_message(
    chat_id,
    from_chat_id,
    disable_notification,
    message_id
)
```

| Parameters            | Type              | Required | Description                                                                                                                     |
|-----------------------|-------------------|----------|---------------------------------------------------------------------------------------------------------------------------------|
| chat\_id              | Integer or String | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                        |
| from\_chat\_id        | Integer or String | Yes      | Unique identifier for the chat where the original message was sent (or channel username in the format @channelusername)         |
| disable\_notification | Boolean           | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound. |
| message\_id           | Integer           | Yes      | Message identifier in the chat specified in from\_chat\_id                                                                      |

#### sendPhoto

Use this function to send photos, using Telegram's `sendPhoto` method.

```Lua
api.send_photo(
    chat_id,
    photo,
    caption,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                              |
|------------------------|----------------------------------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                 |
| photo                  | InputFile or String                                                              | Yes      | Photo to send. Pass a file\_id as String to send a photo that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get a photo from the Internet, or upload a new photo using multipart/form-data. |
| caption                | String                                                                           | Optional | Photo caption (may also be used when resending photos by file\_id), 0-200 characters                                                                                                                                                     |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                          |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                                    |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                           |

#### sendAudio

Use this function to send audio files, using Telegram's `sendAudio` method, if you want Telegram clients to display them in the music player. Your audio must be in the `.mp3` format. Bots can currently send audio files of up to 50 MB in size, this limit may be changed in the future.

```Lua
api.send_audio(
    chat_id,
    audio,
    caption,
    duration,
    performer,
    title,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                                             |
|------------------------|----------------------------------------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                                |
| audio                  | InputFile or String                                                              | Yes      | Audio file to send. Pass a file\_id as String to send an audio file that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get an audio file from the Internet, or upload a new one using multipart/form-data. |
| caption                | String                                                                           | Optional | Audio caption, 0-200 characters                                                                                                                                                                                                                         |
| duration               | Integer                                                                          | Optional | Duration of the audio in seconds                                                                                                                                                                                                                        |
| performer              | String                                                                           | Optional | Performer                                                                                                                                                                                                                                               |
| title                  | String                                                                           | Optional | Track name                                                                                                                                                                                                                                              |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                                         |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                                                   |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                                          |                                                                        |

#### sendDocument

Use this function to send general files, using Telegram's `sendDocument` method. Bots can currently send files of any type of up to 50 MB in size, this limit may be changed in the future.

```Lua
api.send_document(
    chat_id,
    document,
    caption,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                         |
|------------------------|----------------------------------------------------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                            |
| document               | InputFile or String                                                              | Yes      | File to send. Pass a file\_id as String to send a file that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get a file from the Internet, or upload a new one using multipart/form-data. |
| caption                | String                                                                           | Optional | Document caption (may also be used when resending documents by file\_id), 0-200 characters                                                                                                                                          |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                     |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                               |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                      |

#### sendSticker

Use this function to send `.webp` stickers, using Telegram's `sendSticker` method.

```Lua
api.send_sticker(
    chat_id,
    sticker,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                                    |
|------------------------|----------------------------------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                       |
| sticker                | InputFile or String                                                              | Yes      | Sticker to send. Pass a file\_id as String to send a file that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get a `.webp` file from the Internet, or upload a new one using multipart/form-data. |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                                |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                                          |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                                 |

#### sendVideo

Use this function to send video files, using Telegram's `sendVideo` method. Telegram clients support `.mp4` videos (other formats may be sent using `sendDocument`). Bots can currently send video files of up to 50 MB in size, this limit may be changed in the future.

```Lua
api.send_video(
    chat_id,
    video,
    duration,
    width,
    height,
    caption,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                              |
|------------------------|----------------------------------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                 |
| video                  | InputFile or String                                                              | Yes      | Video to send. Pass a file\_id as String to send a video that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get a video from the Internet, or upload a new video using multipart/form-data. |
| duration               | Integer                                                                          | Optional | Duration of sent video in seconds                                                                                                                                                                                                        |
| width                  | Integer                                                                          | Optional | Video width                                                                                                                                                                                                                              |
| height                 | Integer                                                                          | Optional | Video height                                                                                                                                                                                                                             |
| caption                | String                                                                           | Optional | Video caption (may also be used when resending videos by file\_id), 0-200 characters                                                                                                                                                     |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                          |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                                    |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                           |

#### sendVoice

Use this function to send audio files, using Telegram's `sendVoice` method, if you want Telegram clients to display the file as a playable voice message. For this to work, your audio must be in an `.ogg` file encoded with `OPUS` (other formats may be sent as Audio or Document). Bots can currently send voice messages of up to 50 MB in size, this limit may be changed in the future.

```Lua
api.send_voice(
    chat_id,
    voice,
    caption,
    duration,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                                                                               |
|------------------------|----------------------------------------------------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                  |
| voice                  | InputFile or String                                                              | Yes      | Audio file to send. Pass a file\_id as String to send a file that exists on the Telegram servers (recommended), pass an HTTP URL as a String for Telegram to get a file from the Internet, or upload a new one using multipart/form-data. |
| caption                | String                                                                           | Optional | Voice message caption, 0-200 characters                                                                                                                                                                                                   |
| duration               | Integer                                                                          | Optional | Duration of the voice message in seconds                                                                                                                                                                                                  |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                                                                           |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                                                                                     |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user.                                                            |

#### sendLocation

Use this function to send a location on a map, using Telegram's `sendLocation` method.

```Lua
api.send_location(
    chat_id,
    latitude,
    longitude,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters             | Type                                                                             | Required | Description                                                                                                                                                                    |
|------------------------|----------------------------------------------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer or String                                                                | Yes      | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                       |
| latitude               | Float number                                                                     | Yes      | Latitude of location                                                                                                                                                           |
| longitude              | Float number                                                                     | Yes      | Longitude of location                                                                                                                                                          |
| disable\_notification  | Boolean                                                                          | Optional | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                |
| reply\_to\_message\_id | Integer                                                                          | Optional | If the message is a reply, ID of the original message                                                                                                                          |
| reply\_markup          | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user. |

#### sendVenue

Use this function to send information about a venue, using Telegram's `sendVenue` method.

```Lua
api.send_venue(
    chat_id,
    latitude,
    longitude,
    title,
    address,
    foursquare_id,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters              | Type                                                                             | Required   | Description                                                                                                                                                                    |
|-------------------------|----------------------------------------------------------------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id                | Integer or String                                                                | Yes        | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                       |
| latitude                | Float number                                                                     | Yes        | Latitude of the venue                                                                                                                                                          |
| longitude               | Float number                                                                     | Yes        | Longitude of the venue                                                                                                                                                         |
| title                   | String                                                                           | Yes        | Name of the venue                                                                                                                                                              |
| address                 | String                                                                           | Yes        | Address of the venue                                                                                                                                                           |
| foursquare\_id          | String                                                                           | Optional   | Foursquare identifier of the venue                                                                                                                                             |
| disable\_notification   | Boolean                                                                          | Optional   | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                                |
| reply\_to\_message\_id  | Integer                                                                          | Optional   | If the message is a reply, ID of the original message                                                                                                                          |
| reply\_markup           | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional   | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove reply keyboard or to force a reply from the user. |

#### sendContact

Use this function to send phone contacts, using Telegram's `sendContact` method.

```Lua
api.send_contact(
    chat_id,
    phone_number,
    first_name,
    last_name,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters              | Type                                                                             | Required   | Description                                                                                                                                                              |
|-------------------------|----------------------------------------------------------------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id                | Integer or String                                                                | Yes        | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                 |
| phone\_number           | String                                                                           | Yes        | Contact's phone number                                                                                                                                                   |
| first\_name             | String                                                                           | Yes        | Contact's first name                                                                                                                                                     |
| last\_name              | String                                                                           | Optional   | Contact's last name                                                                                                                                                      |
| disable\_notification   | Boolean                                                                          | Optional   | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                                          |
| reply\_to\_message\_id  | Integer                                                                          | Optional   | If the message is a reply, ID of the original message                                                                                                                    |
| reply\_markup           | InlineKeyboardMarkup or ReplyKeyboardMarkup or ReplyKeyboardRemove or ForceReply | Optional   | Additional interface options. A JSON-serialized object for an inline keyboard, custom reply keyboard, instructions to remove keyboard or to force a reply from the user. |

#### sendChatAction

Use this function when you need to tell the user that something is happening on the bot's side, using Telegram's `sendChatAction` method. The status is set for 5 seconds or less (when a message arrives from your bot, Telegram clients clear its typing status).

```Lua
api.send_chat_action(
    chat_id,
    action
)
```

| Parameters    | Type              | Required   | Description                                                                                                                                                                                                                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target channel (in the format @channelusername)                                                                                                                                                                                                               |
| action        | String            | Yes        | Type of action to broadcast. Choose one, depending on what the user is about to receive: typing for text messages, upload\_photo for photos, record\_video or upload\_video for videos, record\_audio or upload\_audio for audio files, upload\_document for general files, find\_location for location data.          |

#### getUserProfilePhotos

Use this function to get a list of profile pictures for a user, using Telegram's `getUserProfilePhotos` method.

```Lua
api.get_user_profile_photos(
    user_id,
    offset,
    limit
)
```

| Parameters    | Type    | Required   | Description                                                                                      |
|---------------|---------|------------|--------------------------------------------------------------------------------------------------|
| user\_id      | Integer | Yes        | Unique identifier of the target user                                                             |
| offset        | Integer | Optional   | Sequential number of the first photo to be returned. By default, all photos are returned.        |
| limit         | Integer | Optional   | Limits the number of photos to be retrieved. Values between 1—100 are accepted. Defaults to 100. |

#### getFile

Use this function to get basic info about a file and prepare it for downloading, using Telegram's `getFile` method. For the moment, bots can download files of up to 20MB in size. The file can then be downloaded via the link `https://api.telegram.org/file/bot<token>/<file_path>`, where `<file_path>` is taken from the response. It is guaranteed that the link will be valid for at least 1 hour. When the link expires, a new one can be requested by calling this method again, also using this function.

```Lua
api.get_file(file_id)
```

| Parameters    | Type   | Required   | Description                       |
|---------------|--------|------------|-----------------------------------|
| file\_id      | String | Yes        | File identifier to get info about |

#### kickChatMember

There are two functions that use this method. One of them kicks the user, and one of them bans the user. By kick, we mean a "soft-ban" - where the user is removed, and then instantly unbanned. A ban means the user cannot return to the group unless unbanned.

To kick a user, use:

```Lua
api.kick_chat_member(
    chat_id,
    user_id
)
```

To ban a user, use:

```Lua
api.ban_chat_member(
    chat_id,
    user_id
)
```

| Parameters    | Type              | Required   | Description                                                                                                     |
|---------------|-------------------|------------|-----------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target group or username of the target supergroup (in the format @supergroupusername) |
| user\_id      | Integer           | Yes        | Unique identifier of the target user                                                                            |

#### unbanChatMember

Use this function to unban a previously kicked user in a supergroup, using Telegram's `unbanChatMember` method. The user will not return to the group automatically, but will be able to join via link, etc. The bot must be an administrator in the group for this to work.

```Lua
api.unban_chat_member(
    chat_id,
    user_id
)
```

| Parameters    | Type              | Required   | Description                                                                                                     |
|---------------|-------------------|------------|-----------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target group or username of the target supergroup (in the format @supergroupusername) |
| user\_id      | Integer           | Yes        | Unique identifier of the target user                                                                            |

#### leaveChat

Use this function for your bot to leave a group, supergroup or channel, using Telegram's `leaveChat` method.

```Lua
api.leave_chat(chat_id)
```

| Parameters    | Type              | Required   | Description                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target supergroup or channel (in the format @channelusername) |

#### getChat

Use this function to get up to date information about the chat (current name of the user for one-on-one conversations, current username of a user, group or channel, etc.), using Telegram's `getChat` method.

```Lua
api.get_chat(chat_id)
```

| Parameters    | Type              | Required   | Description                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target supergroup or channel (in the format @channelusername) |

#### getChatAdministrators

Use this function to get a list of administrators in a chat, using Telegram's `getChatAdministrators` method. If the chat is a group or a supergroup and no administrators were appointed, only the creator will be returned.

```Lua
api.get_chat_administrators(chat_id)
```

| Parameters    | Type              | Required   | Description                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target supergroup or channel (in the format @channelusername) |

#### getChatMembersCount

Use this function to get the number of members in a chat, using Telegram's `getChatMembersCount` method.

```Lua
api.get_chat_members_count(chat_id)
```

| Parameters    | Type              | Required   | Description                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target supergroup or channel (in the format @channelusername) |

#### getChatMember

Use this function to get information about a member of a chat, using Telegram's `getChatMember` method.

```Lua
api.get_chat_member(
    chat_id,
    user_id
)
```

| Parameters    | Type              | Required   | Description                                                                                                            |
|---------------|-------------------|------------|------------------------------------------------------------------------------------------------------------------------|
| chat\_id      | Integer or String | Yes        | Unique identifier for the target chat or username of the target supergroup or channel (in the format @channelusername) |
| user\_id      | Integer           | Yes        | Unique identifier of the target user                                                                                   |

#### answerCallbackQuery

Use this function to send answers to callback queries sent from inline keyboards, using Telegram's `answerCallbackQuery` method. The answer will be displayed to the user as a notification at the top of the chat screen or as an alert.

Alternatively, the user can be redirected to the specified Game URL. For this option to work, you must first create a game for your bot via the [BotFather](https://t.me/BotFather) and accept the terms. Otherwise, you may use links like `telegram.me/your_bot?start=XXXX` that open your bot with a parameter.

```Lua
api.answer_callback_query(
    callback_query_id,
    text,
    show_alert,
    url,
    cache_time
)
```

| Parameters          | Type    | Required   | Description                                                                                                                                                                                                                                                                                                                                        |
|---------------------|---------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| callback\_query\_id | String  | Yes        | Unique identifier for the query to be answered                                                                                                                                                                                                                                                                                                     |
| text                | String  | Optional   | Text of the notification. If not specified, nothing will be shown to the user, 0-200 characters                                                                                                                                                                                                                                                    |
| show\_alert         | Boolean | Optional   | If true, an alert will be shown by the client instead of a notification at the top of the chat screen. Defaults to false.                                                                                                                                                                                                                          |
| url                 | String  | Optional   | URL that will be opened by the user's client. If you have created a Game and accepted the conditions via @Botfather, specify the URL that opens your game – note that this will only work if the query comes from a callback\_game button. Otherwise, you may use links like telegram.me/your\_bot?start=XXXX that open your bot with a parameter. |
| cache\_time         | Integer | Optional   | The maximum amount of time in seconds that the result of the callback query may be cached client-side. Telegram apps will support caching starting in version 3.14. Defaults to 0.                                                                                                                                                                 |

#### editMessageText

Use this function to edit text and game messages sent by the bot or via the bot (for inline bots), using Telegram's `editMessageText` method.

```Lua
api.edit_message_text(
    chat_id,
    message_id,
    text,
    parse_mode,
    disable_web_page_preview,
    reply_markup,
    inline_message_id
)
```

| Parameters                  | Type                 | Required   | Description                                                                                                                                                |
|-----------------------------|----------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id                    | Integer or String    | Optional   | Required if inline\_message\_id is not specified. Unique identifier for the target chat or username of the target channel (in the format @channelusername) |
| message\_id                 | Integer              | Optional   | Required if inline\_message\_id is not specified. Identifier of the sent message                                                                           |
| text                        | String               | Yes        | New text of the message                                                                                                                                    |
| parse\_mode                 | String               | Optional   | Send Markdown or HTML, if you want Telegram apps to show bold, italic, fixed-width text or inline URLs in your bot's message.                              |
| disable\_web\_page\_preview | Boolean              | Optional   | Disables link previews for links in this message                                                                                                           |
| reply\_markup               | InlineKeyboardMarkup | Optional   | A JSON-serialized object for an inline keyboard.                                                                                                           |
| inline\_message\_id         | String               | Optional   | Required if chat\_id and message\_id are not specified. Identifier of the inline message                                                                   |

#### editMessageCaption

Use this function to edit captions of messages sent by the bot or via the bot (for inline bots), using Telegram's `editMessageCaption` method.

```Lua
api.edit_message_caption(
    chat_id,
    message_id,
    caption,
    reply_markup,
    inline_message_id
)
```

| Parameters          | Type                 | Required   | Description                                                                                                                                                |
|---------------------|----------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id            | Integer or String    | Optional   | Required if inline\_message\_id is not specified. Unique identifier for the target chat or username of the target channel (in the format @channelusername) |
| message\_id         | Integer              | Optional   | Required if inline\_message\_id is not specified. Identifier of the sent message                                                                           |
| caption             | String               | Optional   | New caption of the message                                                                                                                                 |
| reply\_markup       | InlineKeyboardMarkup | Optional   | A JSON-serialized object for an inline keyboard.                                                                                                           |
| inline\_message\_id | String               | Optional   | Required if chat\_id and message\_id are not specified. Identifier of the inline message                                                                   |

#### editMessageReplyMarkup

Use this function to edit only the reply markup of messages sent by the bot or via the bot (for inline bots), using Telegram's `editMessageReplyMarkup` method.

```Lua
api.edit_message_reply_markup(
    chat_id,
    message_id,
    inline_message_id,
    reply_markup
)
```

| Parameters          | Type                 | Required   | Description                                                                                                                                                |
|---------------------|----------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id            | Integer or String    | Optional   | Required if inline\_message\_id is not specified. Unique identifier for the target chat or username of the target channel (in the format @channelusername) |
| message\_id         | Integer              | Optional   | Required if inline\_message\_id is not specified. Identifier of the sent message                                                                           |
| inline\_message\_id | String               | Optional   | Required if chat\_id and message\_id are not specified. Identifier of the inline message                                                                   |
| reply\_markup       | InlineKeyboardMarkup | Optional   | A JSON-serialized object for an inline keyboard.                                                                                                           |

#### answerInlineQuery

Use this function to send answers to an inline query, using Telegram's `answerInlineQuery` method. No more than 50 results per query are allowed.

```Lua
api.answer_inline_query(
    inline_query_id,
    results,
    switch_pm_text,
    switch_pm_parameter,
    cache_time,
    is_personal,
    next_offset
)
```

| Parameters            | Type                       | Required   | Description                                                                                                                                                                                                                        |
|-----------------------|----------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inline\_query\_id     | String                     | Yes        | Unique identifier for the answered query                                                                                                                                                                                           |
| results               | Array of InlineQueryResult | Yes        | A JSON-serialized array of results for the inline query                                                                                                                                                                            |
| switch\_pm\_text      | String                     | Optional   | If passed, clients will display a button with specified text that switches the user to a private chat with the bot and sends the bot a start message with the parameter switch\_pm\_parameter                                      |
| switch\_pm\_parameter | String                     | Optional   | Parameter for the start message sent to the bot when user presses the switch button                                                                                                                                                |
| cache\_time           | Integer                    | Optional   | The maximum amount of time in seconds that the result of the inline query may be cached on the server. Defaults to 0.                                                                                                              |
| is\_personal          | Boolean                    | Optional   | Pass True, if results may be cached on the server side only for the user that sent the query. By default, results may be returned to any user who sends the same query                                                             |
| next\_offset          | String                     | Optional   | Pass the offset that a client should send in the next query with the same text to receive more results. Pass an empty string if there are no more results or if you don‘t support pagination. Offset length can’t exceed 64 bytes. |

#### sendGame

Use this function to send a game, using Telegram's `sendGame` method.

```Lua
api.send_game(
    chat_id,
    game_short_name,
    disable_notification,
    reply_to_message_id,
    reply_markup
)
```

| Parameters              | Type                 | Required   | Description                                                                                                                                                  |
|-------------------------|----------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| chat\_id                | Integer              | Yes        | Unique identifier for the target chat                                                                                                                        |
| game\_short\_name       | String               | Yes        | Short name of the game, serves as the unique identifier for the game. Set up your games via Botfather.                                                       |
| disable\_notification   | Boolean              | Optional   | Sends the message silently. iOS users will not receive a notification, Android users will receive a notification with no sound.                              |
| reply\_to\_message\_id  | Integer              | Optional   | If the message is a reply, ID of the original message                                                                                                        |
| reply\_markup           | InlineKeyboardMarkup | Optional   | A JSON-serialized object for an inline keyboard. If empty, one ‘Play game\_title’ button will be shown. If not empty, the first button must launch the game. |

#### setGameScore

Use this function to set the score of the specified user in a game, using Telegram's `setGameScore` method.

```Lua
api.set_game_score(
    chat_id,
    user_id,
    message_id,
    score,
    force,
    disable_edit_message,
    inline_message_id
)
```

| Parameters             | Type    | Required   | Description                                                                                                      |
|------------------------|---------|------------|------------------------------------------------------------------------------------------------------------------|
| chat\_id               | Integer | Optional   | Required if inline\_message\_id is not specified. Unique identifier for the target chat                          |
| user\_id               | Integer | Yes        | User identifier                                                                                                  |
| message\_id            | Integer | Optional   | Required if inline\_message\_id is not specified. Identifier of the sent message                                 |
| score                  | Integer | Yes        | New score, must be non-negative                                                                                  |
| force                  | Boolean | Optional   | Pass True, if the high score is allowed to decrease. This can be useful when fixing mistakes or banning cheaters |
| disable\_edit\_message | Boolean | Optional   | Pass True, if the game message should not be automatically edited to include the current scoreboard              |
| inline\_message\_id    | String  | Optional   | Required if chat\_id and message\_id are not specified. Identifier of the inline message                         |

#### getGameHighScores

Use this function to get data for high score tables, using Telegram's `getGameHighScores` method. Will return the score of the specified user and several of his neighbors in a game.

The method this function uses will currently return scores for the target user, plus two of his closest neighbors on each side. Will also return the top three users if the user and his neighbors are not among them. Please note that this behavior is subject to change.

```Lua
api.get_game_high_scores(
    chat_id,
    user_id,
    message_id,
    inline_message_id
)
```

| Parameters          | Type    | Required   | Description                                                                                |
|---------------------|---------|------------|--------------------------------------------------------------------------------------------|
| chat\_id            | Integer | Optional   | Required if inline\_message\_id is not specified. Unique identifier for the target chat    |
| user\_id            | Integer | Yes        | Target user id                                                                             |
| message\_id         | Integer | Optional   | Required if inline\_message\_id is not specified. Identifier of the sent message           |
| inline\_message\_id | String  | Optional   | Required if chat\_id and message\_id are not specified. Identifier of the inline message   |

## Building Inline Results

There are quite a few types of inline results:

* [InlineQueryResultCachedAudio](https://core.telegram.org/bots/api#InlineQueryResultCachedAudio)
* [InlineQueryResultCachedDocument](https://core.telegram.org/bots/api#InlineQueryResultCachedDocument)
* [InlineQueryResultCachedGif](https://core.telegram.org/bots/api#InlineQueryResultCachedGif)
* [InlineQueryResultCachedMpeg4Gif](https://core.telegram.org/bots/api#InlineQueryResultCachedMpeg4Gif)
* [InlineQueryResultCachedPhoto](https://core.telegram.org/bots/api#InlineQueryResultCachedPhoto)
* [InlineQueryResultCachedSticker](https://core.telegram.org/bots/api#InlineQueryResultCachedSticker)
* [InlineQueryResultCachedVideo](https://core.telegram.org/bots/api#InlineQueryResultCachedVideo)
* [InlineQueryResultCachedVoice](https://core.telegram.org/bots/api#InlineQueryResultCachedVoice)
* [InlineQueryResultArticle](https://core.telegram.org/bots/api#InlineQueryResultArticle)
* [InlineQueryResultAudio](https://core.telegram.org/bots/api#InlineQueryResultAudio)
* [InlineQueryResultContact](https://core.telegram.org/bots/api#InlineQueryResultContact)
* [InlineQueryResultGame](https://core.telegram.org/bots/api#InlineQueryResultGame)
* [InlineQueryResultDocument](https://core.telegram.org/bots/api#InlineQueryResultDocument)
* [InlineQueryResultGif](https://core.telegram.org/bots/api#InlineQueryResultGif)
* [InlineQueryResultLocation](https://core.telegram.org/bots/api#InlineQueryResultLocation)
* [InlineQueryResultMpeg4Gif](https://core.telegram.org/bots/api#InlineQueryResultMpeg4Gif)
* [InlineQueryResultPhoto](https://core.telegram.org/bots/api#InlineQueryResultPhoto)
* [InlineQueryResultVenue](https://core.telegram.org/bots/api#InlineQueryResultVenue)
* [InlineQueryResultVideo](https://core.telegram.org/bots/api#InlineQueryResultVideo)
* [InlineQueryResultVoice](https://core.telegram.org/bots/api#InlineQueryResultVoice)

There is one global function for building an inline result:

```Lua
api.inline_result()
```

You then build the inline result using Lua methods. Each inline query result has a table of fields - some which are required, and some which are optional. The `api.inline_result()` function has a method for every single parameter, where the method name is the parameter name. To build an inline articule result, you'd use:

```Lua
api.inline_result():type('article'):id(1):title('Title'):description('Description'):input_message_content(
    api.input_text_message_content('Text')
)
```

You'll notice a new function inside the input\_message\_content method - this is one of four functions for building the input\_message\_content parameter. This is optional, but it saves you from manually encoding it in JSON.

This object represents the content of a message to be sent as a result of an inline query. Telegram clients currently support the following 4 types:

* InputTextMessageContent
* InputLocationMessageContent
* InputVenueMessageContent
* InputContactMessageContent

To build the InputTextMessageContent object, use the function:

```Lua
api.input_text_message_content(
    text,
    parse_mode,
    disable_web_page_preview
)
```
To build the InputLocationMessageContent object, use the function:

```Lua
api.input_location_message_content(
    latitude,
    longitude
)
```

To build the InputVenueMessageContent object, use the function:

```Lua
api.input_venue_message_content(
    latitude,
    longitude,
    title,
    address,
    foursquare_id
)
```

And to build the InputContactMessageContent object, use the function:

```Lua
api.input_contact_message_content(
    phone_number,
    first_name,
    last_name
)
```

## Building Reply Markup

There are functions to build keyboards and inline keyboards in this API. To build an inline keyboard, use the following function:

```Lua
api.inline_keyboard()
```

For each row of buttons, you need to use the `row()` method on the above function, with the `api.row()` function inside it, like this:

```Lua
api.inline_keyboard():row(
    api.row()
)
```

But wait, this keyboard doesn't have any buttons yet! To add buttons, you need to decide which type of button you are going to use - there are currently 4:

* url
* callback\_data
* switch\_inline\_query
* switch\_inline\_query\_current\_chat

To build a URL button, you need to use the following method with the `api.inline_keyboard()` and the `api.row()` functions:

```Lua
api.inline_keyboard():row(
    api.row():url_button(
        text,
        url
    )
)
```

To build a callback data button, you need to use the following method in the same way as previously shown with the URL button:

```Lua
api.inline_keyboard():row(
    api.row():callback_data_button(
        text,
        callback_data
    )
)
```

To build a switch inline query button, you need to use the following method:

```Lua
api.inline_keyboard():row(
    api.row():switch_inline_query_button(
        text,
        switch_inline_query
    )
)
```

To build a switch inline query current chat button, you need to use the following method:

```Lua
api.inline_keyboard():row(
    api.row():switch_inline_query_current_chat_button(
        text,
        switch_inline_query_current_chat
    )
)
```

To add more buttons to the same row, you need to use the button method again on the `api.row()` function. If I wanted to have an inline keyboard with 2 buttons on the first row and 1 button on the second row, where all of the buttons are URL buttons that display the text "Google" and have the URL "https://www.google.com/", it would look like this:

```Lua
api.inline_keyboard():row(
    api.row():url_button(
        'Google',
        'https://www.google.com/'
    ):url_button(
        'Google',
        'https://www.google.com/'
    )
):row(
    api.row():url_button(
        'Google',
        'https://www.google.com/'
    )
)
```