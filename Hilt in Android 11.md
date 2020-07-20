# Hilt in Android 11

<https://www.youtube.com/watch?v=B56oV3IHMxg>

## What is Dependency Inject? 0:20

See [Dependency Injection on Android](https://youtu.be/o-ins1nvbDg)

## Why is DI hard in Android?

- Framework classes are instantiated by the SDK
- Factories added on APi 28, not realistic

## What about Dagger? 3:10

- Hard to configure
- Multiple paths to do the same thing

49% of developers asked for a DI solution

- Opinionated
- Easy to setup
- Lets you focus on what is important

## Hilt 4:50

- Standardize DI in Android
- Built on top of Dagger
- Tooling Support
- AndroidX Extensions

## Setup 6:10

```kotlin
@HiltAndroidApp
class MusicApp : Application()

@AndroidEntryPoint
class PlayActivity : AppCompatActivity() {

    @Inject lateinit var player: MusicPlayer

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)
        player.play("SDFSDFSD")
    }
}
```

## Hilt Annotations 7:15

- @HiltAndroidApp
- @AndroidEntryPoint
- @InstallIn
- @EntryPoint

```kotlin
@Module
@InstallIn(ApplicationComponent::class)
object DataModule {

    @Provides
    fun provideMusicDB(@ApplicationContext context: Context) =
        Room.databaseBuilder(context, MusicDatabase::class.java, "music.db")
            .build()
}
```

## Components

DI Containers

### Predefined components

- ApplicationComponent (@Singleton) ---> ServiceComponent (@ServiceScoped)
- ActivityRetainedComponent (@ActivityRetained)
- ActivityComponent (@ActivityScoped) ---> ViewComponent (@ViewScoped)
- FragmentComponent (@FragmentScoped) ---> ViewWithFragmentComponent (@ViewScoped)

## Entry Points 12:30

```kotlin
class PlaylistContentProvider: ContentProvider() {

    @EntryPoint
    @InstallIn(ApplicationComponent::class)
    interface PlaylistCPEntryPoint {
        fun getMusicDatabase(): MusicDatabase
    }
}
```

## AndroidX Extensions 14:20

- @ViewModelInject
- @Assisted

## Testing 15:40

- @HiltAndroidTest
- @UninstalledModules
- @BindValue
- @CustomTestApplication

```kotlin
@get:Rule
val rule = HiltAndroidRule(this)

@Before
fun setup() {
    rule.inject(this)
}
```

## Migration 20:00

- Vanilla Dagger & Dagger-Android can work with Hilt
- Hilt has migration APIs
- [Migration Guide](https://dagger.dev/hilt/migration-guide)

## Hilt Anatomy 20:50

- Aggregation

Aggregation of modules and entry points is done across the classpath.

- Android Entry Point
