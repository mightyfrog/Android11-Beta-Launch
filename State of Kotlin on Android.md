# State of Kotlin on Android

<https://www.youtube.com/watch?v=AgPj1Q6D--c>

Why Android loves Kotlin

- Expressiveness
- Safety
- Interoperability
- Structured concurrency

Kotlin on Android in 2020

- 50% of the devs are very satisfied
- 60% pro developers use Kotlin.
- 70% of the top 1K apps contain Kotlin code.

Google's contributions to Kotlin

- Co-leading Kotlin Foundation
- Contributing to the Kotlin compiler
- Developing Kotlin related tooling and libraries

## Java Programming Language

- Android Studio 4.2+ default to language version 8
- Android 11 core library additions
- Core library desugaring (Streams & Times)

## Kotlin at Google

- 1M lines of Kotlin code
- Used on server-side
- Kotlin in OSS projects:
  - gRPC
  - ProtoBuf

## Kotlin 1.4 preview

- Type inference
- Language changes
- More contracts
- Type annotations
- Library changes
- Performance improvements

### Kotlin 1.4 - In a few snippets

```kotlin
fun interface Transformer<T, U> {
    fun transform(x: T): U
}

val length = Transformer {
    x: String -> x.length
}

println(length.transform("a"))
```

```kotlin
val coolNewStuff = listOf(
    "Hello,",
    "world", // allows a trailing comma
)
```

## Kotlin Tooling Performance 7:00

- Incremental annotation processing
- IDE performance
- Gradle improvements
- Kotlin symbol processing

## Kotlin Symbol Processing (KSP) 8:30

[goo.gle/ksp](https://goo.gle/ksp)

- alternative to kapt
- incremental resolution
- 30-40% faster
- multi-platform ready
- Kotlin constructs
- rich set of APIs

## Android 11

### AsyncTask is @Deprecated

Use instead:

- Thread
- Executor
- RxJava
- ListenableFuture
- Coroutines

### Why coroutines

- Structured concurrency
- Non-blocking, sequential code
- Cancellation propagation
- Natural exception handling

### Coroutines & Jetpack

- Lifecycle and ViewModel
- Room
- Work Manager
- Live Data
- Paging
- Compose

## Resources:

[Kotlin Symbol Processing](https://goo.gle/3eJ5fz5)

[Coroutines Codelabs](https://goo.gle/2TS58Jl)

[Coroutines In-depth articles](https://goo.gle/2XcEVay)

[Coroutines videos playlist](https://goo.gle/3gyiGDv)