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

## Managing Shortcuts 3:10

- By default, conversation shortcuts are automatically cached by the system
- Developers can remove these using removeLongLivedShortcuts()
- Cached shortcuts should only be removed if users can no longer access these conversations in your app

## Conversation Notifications 3:40

- In Android 11, notifications that refer to valid conversation shortcuts and use MessagingStyle appear above other notification
- Conversation shortcuts allow a chat to be bubbled from a notification, and on long-press, users can mark it as priority

