# Conversation notifications New features & best practices

<https://www.youtube.com/watch?v=CKlVSvFyt3s>

## Goal

The people in your life feel like they're right there with you.

## Conversation Shortcuts 1:00

- Publish a shortcut and set as "long lived" to use across OS features
- Meant for real-time conversations with real people, for like chat apps
- Avoid for bots, ost updates, and on way announcements

```kotlin
// first, build a Person object with details about the contact
val person = Person.Builder()
    .setName(...)
    .setIcon(personIcon)
    .build()

// the, create a Shortcut referencing your Person object and set as long lived
val shortcut = ShortcutInfo.Builder()
    .setLongLived(true) // important
    .setIntent(...)
    .setShortLabel(...)
    .setIcon(personIcon)
    .setPerson(person)
    .setCategories(...)
    ...
    .build()

// automatically handles shortcut publish limits
context.getSystemService(Context.SHORTCUT_SERVICE
    .pushDynamicShortcut(shortcut)
```

### Managing Shortcuts 3:10

- By default, conversation shortcuts are automatically cached by the system
- Developers can remove these using removeLongLivedShortcuts()
- Cached shortcuts should only be removed if users can no longer access these conversations in your app

### Conversation Notifications 3:40

- In Android 11, notifications that refer to valid conversation shortcuts and use MessagingStyle appear above other notification
- Conversation shortcuts allow a chat to be bubbled from a notification, and on long-press, users can mark it as priority

- Conversation shortcuts allow a chat to be bubbled from anotificatio, and on long-press, users can mark it as priority

### Adding Conversation Shortcuts to Notifications

```kotlin
val notification = Notification.Builder(
    context, conversationChannelId)
    .setStyle(Notification.MessagingStyle.Builder(person))
        .addMessage(...)
        ...
        .build())
    .setShortcutId(conversationShortcutId)
    ...
    .build()
```

### Conversation Notifications without Shortcuts 5:10

- Until apps target Android 11, MessagingStyle Notifications that do not refer to a conversation shortcut will still appear above other notifications
- However, users won't be able to bubble these or mark them as priority

## Bubbles 5:40

### Bubbles and Conversations

- Bubbles make it easy to get back to conversations without switching apps
- Users can select conversations to bubble right from the notification
- Apps can reuse your conversation shortcut to construct bubble metadata

### Using Conversation Shortcuts to create Bubbles 6:20

```kotlin
val bubble = Notification.BubbleMetadata.Builder(conversationShortcutId)
    .build()
    val notification = Notification.Builder(
            context, conversationChannelId)
            .setStyle(Notification.MessagingStyle.Builder(person))
            ...
            .build())
        .setShortcutId(conversationShortcutId)
        ...
        .setBubbleMetadata(bubble) // set bubble
        .build()
```

### Bubble Permissions 6:40

- Users create bubbles in two ways:
  1. Specific chats: by tapping the bubble button on the notification
  2. All chats: requires users to adjust the app bubble setting
- You must provide bubble metadata to create a bubble. You should do so for appropriate conversations

### Tips for Bubble activities 8:00

- Bubble activities should be resizable
- Focus bubbles on single tasks and minimize navigation to other parts of the app within the bubble
- Actions which result in creating a new task will collapse the bubble window and redirect to a full screen experience

