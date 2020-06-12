# All things privacy in Android 11

<https://www.youtube.com/watch?v=5w5h_UdIiqs>

## One-time permissions 2:00

The granted permission remains active while the app is in the foreground. The permission is revoked shortly after the app goes background.

### Permission best practices

```kotlin
when {
    checkSelfPermission(...) == GRANTED -> {
        // perform action
    }
    shouldShowRequestPermissionRationale(...) -> {
        // show in-context rationale
    }
    else -> {
        requestPermissions(...)
    }
}
```

When reminded of background access many users action change from "all the time". Over 65% choose to restrict access "while in use" or "deny".

### Foreground location request

ACCESS_COARSE_LOCATION or ACCESS_FINE_LOCATION

```kotlin
ActivityCompat.requestPermissions(
    /* Activity */ this,
    /* Request BG alone */
    arrayOf(ACCESS_COARSE_LOCATION),
    /* RequestCode */ 100
)
```

- Requests foreground access on all Android versions
- On Android 9 and lower, background access is implied

## Many apps go unused 4:20

Average smartphone user has 75 apps on their device and only interact with a third.

By default for apps targeting Android 11, Android will automatically revoke all runtime permissions if the app is not used for a certain amount of time.

Notification will let users know permissions have been removed.

## New foreground service type for apps targeting 11 5:45

```xml
<manifest>
   ...
   <service ... android:foregroundServiceType="camera" />
</manifest>
```

## Scoped storage summary 6:40

- Internal and external app storage is unreadable by other aps on the device and accessible to the app owner without user permission
- Apps can contribute to organized Media and Downloads collections without permission (mandatory for apps targeting 11)
- Apps need user consent to modify or delete media files it does not own (mandatory for apps targeting 11)
- Apps with the media permission can also read media using file path and native libraries (mandatory for apps targeting 11)
- Apps with special app access can manage all files in shared storage (new in 11)

[Storage access with Android 11](https://www.youtube.com/watch?v=RjyYCUW-9tY)

## Apps targeting Android 11 will no longer be able to see other installed apps 7:55

Apps targeting 10 and earlier will retain the ability to view all packages on device.

### Query and interact with a specific app

```xml
<manifest>
    <queries>
        <package android:name="com.example.store" />
        <package android:name="com.example.service" />
    </queries>
</manifest>
```

### Query and interact with app based on intent filter

```xml
<manifest>
    <queries>
        <intent>
            <action android:name="android.intent.action.SEND" />
            <data android:mimeType="image/jpeg" />
        </intent>
    </queries>
</manifest>
```

## Controlling how your intent is handled 9:40

```kotlin
val url = "https://news/google.com"
val intent = Intent(ACTION_VIEW, Uri.parse(url).apply {
    flags = FLAG_ACTIVITY_NEW_TASK or FLAG_ACTIVITY_REQUIRE_NON_BROWSER
})
startActivity(intent)
```

Android 11 introduces FLAG_ACTIVITY_REQUIRE_NON_BROWSER so the intent handler ignores browsers.

## Give developers insights into data access 10:40

### Two new APIs

- Callback on data access: Easily identify code that is accessing gated permissions even within 3rd party SDKs

- Feature tagging: Reduce the amount of time to identify features trhat are executing code accessing dangerous permissions

### Setting up data access operation callback

```kotlin
val appOpsManager = getSystemService(AppOpsManager::class.java) ...
appOpsManager.setOnOpNotedCallback(mainExecutor, appOpsCallback)
```
