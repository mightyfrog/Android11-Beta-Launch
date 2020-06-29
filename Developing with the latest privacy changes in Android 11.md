# Developing with the latest privacy changes in Android 11

<https://www.youtube.com/watch?v=MXlVj-EYgIQ>

[All things privacy in Android 11](https://www.youtube.com/watch?v=5w5h_UdIiqs)

## Privacy Principles 1:15

1. Request minimum permissions
2. Only ask for access in context
3. Plan for users ot select deny
4. Don't access data when the user doesn't expect it
5. Pay attention to libraries

### #1 Request the minimum permission that your app's feature needs

Use Android vitals to gauge user perception.

### #2 Request permission sonly when eeded and be transparent

1. checkSelfPermission(...)
2. shouldShowRequestPermissionRationale(...)
3. requestPermission(...)

### #3 Plan for the user or the system to "Deny" your permissions

One time permission lives as long as the app is in the foreground.

Automatic revoke of permissions from unused apps

Handling "Deny"

1. Gracefully degrade, don't block users from your app
2. Expect permanent deny, don't push users to Settings
3. Use **shouldShowRequestPermissionRationale** properly

### #4 Do not access private data if the user doesn't expect it

Data usage audit APIs

- **Callback on data access** Easily identify code that is accessing gated permissions even within 3rd party SDKs
- **Feature tagging** Reduce the amount of time to identify features that are executing code accessing dangerous permissions

What code uses sensitive data & how much?

```kotlin
val appOpsManager =
    getSystemService(AppOpsManager::class.java)
appOpsManager.setOnOpNotedCallback(mainExecutor, appOpsCallback)
```

The callback provides the type of data being accessed and records frequency and timing of acccess.

### #5 Pay attention to permission required by libraries

### One  last tip

[ActivityRequestContract.RequestPermission](https://developer.android.com/reference/androidx/activity/result/contract/ActivityResultContracts.RequestPermission?hl=en)
