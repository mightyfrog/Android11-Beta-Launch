# What's new in System UI

<https://www.youtube.com/watch?v=oLLUDOQxJS8>

## Conversations 0:50

Our user research and experience tell us that notifications to and from people are the most important to us. (Google I/O '17)

Android 11 adds a dedicated persistent space for these notifications so you can quickly see and respond to them.

Android 11 notification has:

- separate section headers
- message count indicator
- sender & chat names clearly scannable
- prominent primary action
- avatars are bigger & up front

Long press to mark conversations as important so they appear first and break through do-not-disturb.

### Conversation API

1. Use MessagingStyle
2. Add a shortcut ID

that's it.

```kotlin
val person = Person.Builder()
    .setName(...)
    .build()

val shortcut = ShortcutInfo.Builder(context, conversationShortcutId)
    .setLongLived(true)
    .setPerson(person)
    .build()
context.getSystemService(Context.SHORTCUT_SERVICE)
    .pushDynamicShortcut(shortcut)

val notification = Notification.Builder(context, conversationChannelId)
    .setStyle(Notification.MessagingStyle.Builder(person))
        .addMessage(...)
        ...
        .build())
    .setShortcutId(conversationShortcutId)
    ...
    .build()
```

## Bubbles 3:50

Bubbles have graduated from developer preview to full user feature! The user can grab a conversation that they're actively engaging with and multitask with it using a bubble.

```kotlin
val bubs = Notification.BubbleMetadata.Builder(shortcutId) // *1
    .setIntent(conversationPendingIntent) // *2
    .build()

val notification = Notification.Builder(context, conversationChannelId)
    .setStyle(messagingStyle)
    .setShortcutId(conversationShortcutId)
    ...
    .setBubbleMetadata(bubbs)
    .build()
```

*1 the bubble will automatically use the shortcut's (adaptive) icon

*2 this activity should be prepared to be resized

## Device Controls 5:50

Android 11 creates a dedicated persistent space for home and IoT controls so you can quickly find and use them at any time.

We've redesigned the power menu to place your device controls just a (long) button-press away.

You can choose the set of controls that will show up there and even switch between providers.

### android.service.controls.*

A brand new API for device-controls providers. One of our first Java 9 Reactive Streams APIs.

### ControlsProviderService

```kotlin
override fun createPublisherForAllAvailable() : Flow.Publisher<Control> {
    // create and return a Publisher that offers every control
    // that the user could possibly want to favorite
}

override fun createPublisherFor(list: MutableList<String>) : Flow.Publisher<Control> {
    // create and return a Publisher that cna push updates
    // to a specific set of controls (chosen by the user, in System UI)
}
```

```kotlin
override fun performControlAction(controlId: String, action: ControlAction, consumer: Consumer<Int>) {
    // the user interacted with a control!

    // user controlId and action to figure out what to change in the real
    // world and tell your publishers to push any updated state to
    // System UI with Flow.Subscriber#onNext()

    // make sure you let SYstem UI know it worked
    consumer.accept(ControlAction.RESPONSE_OK)
}
```

### Control example

```kotlin
private fun createBulbControl(isOn: Boolean) : Control {
    // the user can long-press to launch this intent
    val pendingIntent = PendingIntent.getActivity(...)

    val toggleTemplate = ToggleTemplate("toggle", ControlButton(isOn, getString(R.string.desc)))

    // this light bulb has state: a simple on/off toggle
    val builder = Control.StatefulBuilder(uniqueId, pendingIntent)
        .setTitle("My Lump")
        .setDeviceType(DeviceTypes.TYPE_LIGHT) // lots to choose from!
        .setControlTemplate(toggleTemplate)
        .setStatus(Control.STATUS_OK) // don't forget this!

    return builder.build()
}
```

## Media Player 7:20

API 21 introduced MediaStyle notification.

Android 11 creates  dedicated persistent space for media players so you can quickly control and even resume content.

Media controls live in:

- Lockscreen
- "Quick" Quick Settings
- Quick Settings

If you implement MediaBrowserService, your card can be persistent! This is so the user can resume playback later - after your MediaSession is destroyed, or even after a reboot.

