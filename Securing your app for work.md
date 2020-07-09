# Securing your app for work

<https://www.youtube.com/watch?v=2y9Ol2N1I4k>

## Data Encryption on Android

## Jetpack Security 1:30

Can be broken up in two sections:

- Key Management
- Data Encryption

### AndroidKeyStore

$1,000,000 bounty for breaking the Titan M chip.

### MasterKey AES256 example

```kotlin
val keyAlias = MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC)
```

### MasterKey w/auth example

```kotlin
val advancedSpec = KeyGenParameterSpec.Builder(
    "master_key",
    KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT
).apply {
    setBlockModes(KeyProperties.BLOCK_MODE_GCM)
    setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
    setKeySize(256)
    setUserAuthenticationRequired(true)
    setUserAuthenticationValidityDUrationSeconds(15)
    if (Build.VERSION.SDK_IN >= Build.VERSION_CODES.P) P{
        setUnlockedDeviceRequired(true)
        setIsStrongBoxBacked(true)
    }
}.build()
val advancedKeyAlias = MasterKeys.getOrCreate(advancedSpec)
```

## KEy Authorization

- Require Device Unlock
- Key use authorization for time-bound keys

### BiometricPrompt Key Auth

```kotlin
val promptInfo = PromptInfo.Builder()
    .setTitle("Unlock?")
    .setDescription("Would you like to unlock this key?")
    .setDeviceCredentialAllowed(true)
    .build()

BiometricPrompt(
    this, // activity
    ContextCompat.getMainExecutor(this),
    authenticationCallback
).authenticate(promptInfo)

private val authenticationCallback = object : AuthenticationCallback() {
    override fun onAuthenticationSucceeded(result: AuthenticationResult) {
        super.onAuthenticationSucceeded(result)
        // Unlocked -- do work here.
    }

    override fun onAuthenticationError(
        errorCode: Int,
        errString: String
    ) {
        super.onAuthenticationError(errorCode, errString)
        // Handle error
    }
}
```

## EncryptedFile

- Uses streaming AES encryption to handle files of all sizes
- Use like a normal file with minor exceptions

```kotlin
val secretFile = File(filesDir, "super_secret")
val encryptedFile = EncryptedFile.Builder(
    secretFile,
    applicationContext,
    advancedKeyAlias,
    FileEncryptionScheme.AES256_GCM_HKDF_4KB)
    .setKeysetAlias("file_key") // optional
    .setKeysetPrefName("secret_shared_prefs") // options
    .build()

encryptedFile.openFileOuptut().use { outputStream ->
    // Write data to your encrypted file
}

encryptedFile.openFileInput().use { inputStream -> 
    // Read data from your encrypted file
}
```

### EncryptedSharedPreferences

- Keys are encrypted deterministically
- Implements SharedPreferences for interop

```kotlin
EncryptedSharedPreferences.create(
    "my_secret_prefs",
    advancedKeyAlias,
    applicationContext,
    PrefKeyEncryptionScheme.AES256_SIV,
    PrefValueEncryptionScheme.AES256_GCM
).edit {
    // Update secret values
}
```
