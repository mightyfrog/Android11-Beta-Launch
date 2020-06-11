# What's new in Android

[https://www.youtube.comwatchv=fnkFOhA7FC4](https://www.youtube.comwatchv=fnkFOhA7FC4)

## Window Insets 1:00

More information about the multiple types of content being displayed

- Status, navigation, IME...

```kotlin
// getWindowInsets object from listener
view.setOnApplyWindowInsetsListener { view, insets ->
    // See if the IME is visible
    val imeVisible = insets.isVisible((WindowInsets.Type.ime()))
    if (imeVisible) {
        val imeInsets = insets.getInsets(WindowInsets.Type.ime())
        // ...
    }
}
```

## IME Animations 2:28

[WindowInsetsAnimation app](https://goo.gle/insetsanimsample)

[Android Developer Backstage #138: Animated IME - Oh, my!](https://androidbackstage.blogspot.com/2020/04/episode-138-animated-ime-oh-my.html)

- Synchronize keyboard animations with app content changes
  - Listenf or changes AND/OR
  - Drive keyboard animation directly

- Listening for keyboard changes

```kotlin
editText.setWindowInsetsAnimationCallback(animCallback)

val animCallback = object : WindowInsetsAnimation.Callback(DISPATCH_MODE_STOP) {
    override fun onProgress(p0: WindowInsets, p1: MutableList<WindowInsetsAnimation>): WindowInsets {
        ...
    }

    // Optional overrides
    override fun onPrepare(animation: WindowInsetsAnimation) {
        ...
    }

    override fun onEnd(animationWindowInsetsAnimation) {
        ...
    }

    override fun onStart(animation: WindowInsetsAnimation) {
        ...
    }
}
```

- Animating the keyboard directly

```kotlin
editText.windowInsetsController?.controlWindowInsetsAnimation(
    WindowInsets.Type.ime(), /* animate the keyboard */
    -1,                      /* infinite duration */
    linearInterpolator,      /* linear motion */
    cancellationSignal,      /* allows cancellation */
    animationControlListener /* ready/cancelled/finished */
)
```

## Conversations 5:30

- Create Conversation

```kotlin
// Create and post shortcut
val person = Person.Builder().build()
val shortcutInfo = ShortcutInfoCompat.Builder(this, "sampleShortcut")
    .setPerson(person)
    .setLongLived(true)
    // ...
    .build()
ShortcutManagerCompat.pushDynamicShortcut(shortcutInfo)

// Create notification with shortcut
val style = NotificationCompat.MessagingStyle(person)
    .addMessage(...)
    // ...
NotificationCompat.Builder(this, "foo")
    .setShortcutId(shortcutInfo.id)
    .build()
```

## Bubbles 6:35

[Android Samples](https://github.com/android/user-interface-samples)

[Android Developer Backstage #140: Bubbles!](https://androidbackstage.blogspot.com/2020/06/episode-140-bubbles.html)

- Notification that can also show as bubbles
- Android 10: Developer option
  - Android11: They're here!
- Better thatn System Alert Window!
- Created with Notification API
  - with more metadata
  - and dedicated activity

### Bubbles: Manifest

Manifest:

```xml
<activity
    android:name=".bubles.BubbleActivity"
    android:theme="@style/AppTheme.NoActionBar"
    android:label="@string/title_activity_bubble"
    android:resizeableActivity="true"
/>
```

Code:

```kotlin
    // Create Intent to launch
    val intent = Intent(context, BubbleActivity)
    val bubbleIntent = PendingIntent.getActivity(context, 0, intent, ...)

    // Create metadata
    val shortcutInfo = ... /* probably already using for notifications */
    val bubbleMetadata = Notification.BubbleMetadata.Builder(shortcutInfo.id)

    // Create Notification with metadata
    val builder: Notification.Builder = Notification.Builder(context, CHANNEL_ID)
        // ..
        .setBubbleMetadata(bubbleMetadata)
        .setCategory(...)
        .setShortcutId(...)
```

## Privacy 9:20

[All things privacy in Android 11](https://www.youtube.com/watch?v=5w5h_UdIiqs&list=PLWz5rJ2EKKc9hqKx4qZWolQxy59Bt20t_&index=7&t=0s)

### Data Access Auditing

- Callbacks invoked when user-permission-required data is accessed

```kotlin
val appAOpsCallback = object : AppOpsManager.OnOpNotedCallback() {

    override fun onNoted(syncNotedAppOp: SyncNotedAppOp) {
        ...
    }

    override fun onSelfNoted(syncNotedAppOp: SyncNotedAppOp) {
        ...
    }

    override fun onAsyncNoted(syncNotedAppOp: SyncNotedAppOp) {
        ...
    }
}

val appOpsManager = getSystemService(AppOpsManager::class.java) as AppOpsManager
appOpsManager.setOnOpNotedCallback(mainExecutor, appOpsCallback)
```

- One-Time Permissions

Adds "Only this time" to the permission request dialog.

- Background Location
  - More restrictive in Android 11
  - First, request foreground permission
  - Then request background permission
    - Takes user to Settings

- Foreground Services
  - Android 10 required manifest attribute for Location
  - Android 11 adds
    - Camera
    - Microphone

### Foreground Services Type

```xml
<manifest>
   ...
   <service ... android:foregroundServiceType="camera|microphone" />
   ...
</manifest>
```

- But Wait, There's More!
  - Package visibility restrictions
  - Scoped storage
  - Auto-reset permissions

[All things privacy in Android 11](https://www.youtube.com/watch?v=5w5h_UdIiqs&list=PLWz5rJ2EKKc9hqKx4qZWolQxy59Bt20t_&index=7&t=0s)

[Modern Storage on Android](https://www.youtube.com/watch?v=RjyYCUW-9tYlist=PLWz5rJ2EKKc9hqKx4qZWolQxy59Bt20t_&index=8&t=0s)

## Wi-Fi Debugging 14:00

## Nullability Annotations 15:00

- @RecentlyNullable
- @RecentlyNonNull
  - Warnings
- @Nullable, @NonNull
  - Errors

## Crash Reasons Reporting 16:00

- API to query why your app crashed
  - Upload reports

### Crash Reason Querying

```kotlin
// Returns List of ApplicationExitInfo
val reasonsList = activityManager.getHistoricalProcessExitReasons(
    packageName, pid /* 0 for all matches */, max /* 0 for all */
)
for (info in reasonsList) {
    // Log/store/upload info.reason
    // REASON_LOW_MEMORY, REASON_CRASH, REASON_ANR, etc.
}
```

## GWP-ASan 17:00

- For native code/libraries
- Android 10: HWASan
- GWP-San
  - Catches memory issues
  - On user devices in the field
  - Low overhead (runtime and memory)
  - Reports uploaded to Play dashboard

### How to enable GWP-ASan

manifest:

```xml
<application android:gwpAsanMode="always">
```

## ADB Incremental 18:35

- Faster install via command-lines
- For huge APKs (think: games)
- Up to 10x faster

```shell
// First: sign APK, create APK Signature Scheme v4 file
// Then, run ADB incremental
$ adb install --incremental
```

## Behavior Changes 19:20

- Most changes limited to targetSdk R
- Test changes with behavior toggles
  - Command-lines
  - New Developer Options panel

### Toggling Behavior Changes

- Via UI or
- Via Command-Line

```shell
// adb shell am compat (enabled|disable) (CHANGE_ID|CHANGE_NAME) PACKAGE_NAME
$adb shell am compat disable DEFAULT_SCOPED_STORAGE com.android.samples.android11playground
```

## NDK Image Decoders 20:00

- All decoders available from native code
  - Jpeg, gif, PNG, WebP,...
- No more
  - JNI up-calling
  - BUndling decoder libraries
  - Bulking APK size

## Animated HEIF 21:00

- Load animated images from HEIF files
  - Loaded as AnimatedImageDrawable
    - Like animated GIFs

```kotlin
val file = File("someHeifFile")
val source = ImageDecoder.createSource(file)

// Perform off the main thread
val drawable = ImageDecoder.decodeDrawable(source)
if (drawable is AnimatedImageDrawable) {
    drawable.start()
}
```

## NDK: OpenSL ES 21:40

OpenSL ES has been @Deprecated

Use Oboe instead

- Oboe for the wind!
- Unbundled C++ library
  - High-performance audio
  - Works back to API 16
  - Open source
    - github.com/google/oboe

[Android Developer Backstage #135: Audio Podcast](https://androidbackstage.blogspot.com/2020/04/episode-135-audio-podcast.html)

## Variable refresh rate 22:30

- For apps with their own rendering loop
  - e.g. games
- 60 frames per second used to be a given
  - Now some devices support 90, 120Hz
  - Enables more flexible backoff rates
- Surface.setFrameRate() // sets "hint"

## Neural Networks API 23:48

- C API for on-device machine learning
- NNAPI 1.3: Performance nad functionality
  - Control flow ops
  - Async command-queue APIs
  - Hard-swish op

## 5G 24:40

- APIs to optimize 5G experience
  - Metered network state
  - Bandwidth estimates

```kotlin
val manager = getSystemService(ConnectivityManager::class.java)
manager.registerDefaultNetworkCallback(object : ConnectivityManager.NetworkCallback() {
    override fun onCapabilitiesChanged(network: Network, capabilities: NetworkCapabilities) {
        if (capabilities.hasCapability(NetworkCapabilities.NET_CAPABILITY_NOT_METERED)) {
            ...
        }
        if (capabilities.linkDownstreamBandwidthKbps > FAST_NETWORK) {
            ...
        }
    }
})
```

## Autofill/Keyboard Integration 25:05

- Autofill content in keyboards, not just drop-down
  - Keyboards apps
  - Password apps

### Autofill: Keyboard

```kotlin
// Imlement InputMethodService methods

// Autofill request from IME
override fun onCreateInlineSuggestionsRequest(uiExtras: bundle): InlineSuggestiongsRequest? {
    // ...
}

// Autofill response
override fun onInlineSuggestionsResponse(response: InlineSUggestionsResponse): Boolean {
    // ...
}
```

### Autofill: Password Apps

```kotlin
// AutofillServices handle onFillRequest(), create FillResponse with InlinePresentation

override fun onFillRequest(...) {
    val datasetBuilder = Dataset.Builder()
    val inlinePresentation = InlinePresentation(...)
    datasetBuilder.setValue(..., inlinePresentation, ...)
    responseBulder = FillResponse.Builder()
    val fillResponse = responseBuilder.addDataset(datasetBuilder.build())
        //...
        .build()
}
```

## Jetpack 26:55

- 70+ libraries, releases every two weeks
- New/Recent
  - Hit; DI built on Dagger
  - Paging 3.0: All Kotlin, with coroutines
  - CameraX Beta
  - and more!

[What's new in Android Jetpack](https://www.youtube.com/watch?v=R3caBPj-6Sg)

## Jetpack Compose 27:40

- New UI Toolkit for Android
- Reactive, Kotlin-based
- Pre-alpha, developed in the open

[Jetpack Compose](https://www.youtube.com/watch?v=U5BwfqBpiWU)

## Android Studio

- 4.0: Stable
  - Motion Editor
  - LayoutInspector

- 4.1: Beta
  - Database Inspector (Room, SQLite)

- 4.2: Canary
  - Wireless debuggin with Android 11
  - Jetpack Compose development

[What's new in Android Development Tools](https://www.youtube.com/watch?v=NMFGuy6TRqk)

[What's new in Design Tools](https://www.youtube.com/watch?v=ns67AAuDs4s&list=PLWz5rJ2EKKc9hqKx4qZWolQxy59Bt20t_&index=12&t=0s)

## Google Play 29:45

- New Play console: complete redesign
  - clearer, easier to use
  - policy status section
  - acquisition reports
  - team maanagement

- Now in Beta
  - [play.google.com/console](https://play.google.com/console)

## What's Next? 30:00

[Android 11 Beta Launch Show](https://youtube.com/androiddevelopers)

[Android 11 Preview Site](https://d.android.com/preview)

[11 Weeks of Android](https://g.co/android11)

[Android 11 Meetups](https://d.android.com/11meetups)

### Now in Android

- video: <https://youtube.com/androiddevelopers>
- blog: <https://medium.com/androiddevelopers>
- podcast: <https://goo.gle/nia-podcast>
