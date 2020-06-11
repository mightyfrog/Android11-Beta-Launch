# What's new in Android Jetpack

<https://www.youtube.com/watch?v=R3caBPj-6Sg>

47% of the top 1K apps use Jetpack libraries.

## Hilt 1:00

Jetpack's recommended DI library.

### Why DI?

- Greater code reusability
- Ease of testing
- 49% of developers asked for a DI solution

### Why Dagger?

74% of the top 10K apps use Dagger.

```kotlin
@AndroidEntryPoint
class SearchFragment : Fragment() {
    @Inject
    lateinit var foo : Foo
    val viewModel: SearchViewModel vy viewModels<>()
}

@AndroidEntryPoint
class MainActivity : AppCompatActivity()

@AndroidEntryPoint
class MyService : Service()

@HiltAndroidApp
class MyApplication : Application()

class SearchViewModel @ViewModelInject constructor(
    private val database: MyDatabase
): ViewModel()
```

```kotlin
@InstallIn(ApplicationComponent::class)
@Module
object AppModule {
    @Provides
    fun provideDb(app: Application) : MyDatabase {
        ...
    }
}
```

### Pre-defined Scopes

- Application
- Activity
- Fragment
- Service
- View
- Retained
- ...

### Hilt in Action

Replaced Dagger with Hilt in the Google I/O app and 75% of DI code was deleted.

### Hilt: Jetpack-Recommended DI on Android

- Build on Dagger
- Jetpack Integrations
- Scopes for Android
- Testing APIs
- Studio Integration

## App Startup 4:30

Faster application initialization

Content Provider creation is expensive.

```kotlin
class WorkManagerInitializer : Initializer<WorkManager> {
    override fun create(context : Context) : WorkManager {
        val configuration = Configuration.Builder()
            .setMinimumLoggingLevel(Log.DEBUG)
            .build()
        WorkManager.initialize(context, configuration)
        return WorkManager.getInstance(context)
    }

    override fun dependencies() : List<Class<out Initializer<*>>> = emptyList9)
}
```

### Speeding up initialization

- Both App & Library Developers
- Optional Lazy Initialization
- Automatically added Trace points

## Android Game SDK 6:20

### Maximize Game Perf

- Frame pacing
- Performance Tuner

## Benchmark 1.1 7:20

- Support for CPU profiling benchmarks
- See how many allocations you make

## Paging 3 7:50

Gradual and graceful data loading

### PagingSource

```kotlin
class MyPagingSource : PagingSource<Key, Value>() {
    override suspend fun load(params: LoadParams<Key>): LoadResult(Key, Value> {
        try {
            val result = api.requestPage(params.key)
            return Page(
                data = result.items,
                nextKey = result.nextKey
            )
        } catch (error: IOException) {
            return Error(error)
        }
    }
}
```

### Pager

```kotlin
val pagingFlow = Pager(PagingConfig(pageSize = 10)) {
    MyPagingSource()
}.flow.cachedIn(viewModelScope)
```

### Presenting

```kotlin
// Activity
recyclerview.adapter = MyPagingAdapter()
lifecycleScope.launch {
    viewModel.pagingFlow.collectLatest {
        adapter.submitData(it)
    }
}
```

### Built Kotlin-first

- Kotlin Coroutines & FLow
- Headers & Footers
- Loading State & Retry
- Compatible with Paging 2

## CameraX 9:20

Build camera apps quickly with awesome reliability

### Beta

- Runs on 90% of Android Test Lab covering ~400M devices
- Seamless preview on any screen with PreviewView
- Easy YUV to RGB translation for smart imaging applications

## WorkManager 10:50

Deferrable background jobs

### WorkManager 2.3 & 2.4

- Better in-process scheduler

### Foreground Services

```kotlin
// Worker
override suspend fun doWork(): Result {
    // Bind Workers' lifecycle to a Foreground Service
    setForeground(ForegroundInfo(
        R.id.workerNotificationId,
        buildNotification()
    ))
    doSomeWork()
    return Result.success()
}
```

### Better Diagnostics

```shell
// Dump diagnostics to logcat
adb shell am broadcast -a "androidx.work.diagnostics.REQUEST_DIAGNOSTICS" -p "<your_package_name>"

// View Diagnostics
adb logcat
```

### Lint Checks

```kotlin
private class TestWorker(context: Context, parameters: WorkerParameters) :
    CoroutineWorker(context, parameters) {
        override suspend fun doWork(): Result {
            return Result.success()
        }
    }
}
```

Lint immediately identifies TestWorker must not be private.

## Navigation 12:50

A library for structuring your in-app UI according to design guidelines

### Navigation 2.3

- Dynamic feature modules
  - Annotate fragments with module names
- Deep Linking improvements
- Returning Results

### Returning a Result

```kotlin
val savedStateHandle = navController.previousBackStackEntry?.savedStateHandle
// Set the result
savedStateHandle?.set("key", result)
```

### Receiving a Result

```kotlin
override fun onViewCreated(view: View, savedInstanceState: BUndle?) {
    val savedStateHandle = findNavController().currentBackStackEntry?.savedStateHandle
    savedStateHandle?.getLiveData<String>("key")
        .observe(viewLifecycleOwner) { result ->
            // ...
        }
}
```

## startActivityForResult API 14:55

- Making permissions easier

### ActivityResultContracts API

- New APIs replacing startActivityForResult
- Type safe contracts for common intents

### Requesting Permissions

```kotlin
val requestPermission = registerForActivityResult(RequestPermission()) { granted ->
    if (granted) {
        // ...
    }
}

override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    val mapButton =- findViewById<Button>(R.id.map_button)
    mapButton.setOnClickListener {
        requestPermission.launch(ACCESS_FINE_LOCATION)
    }
}
```

You can request multiple permissions at once too.

### More ActivityResultContracts

- Take picture
- Get content
- Open document

## AppCompat 16:10

Getting the newest Android on older devices

### App Compat Lint Rules

### AppCompat & Dark Mode

- More reliable Dark Theme
- Configuration overrides API

### Using a Custom Locale

```kotlin
override fun attachBaseContext(context: Context) {
    // create new configuration
    val config = Configuration()
    // apply custom locale
    config.local = myCustomLocale
    // Workaround for platform bug on SDK < 26
    config.fontScale = 0f
    // create new context
    val updatedContext = context.createConfigurationContext(config)
    super.attachBaseContext(updatedContext)
}
```

### Webkit & Dark Mode

- Forced dark mode support for WebView in 1.2.0
