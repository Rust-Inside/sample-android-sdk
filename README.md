# Sample Rust Core · Android SDK

[한국어](README.ko.md)

Call `account.getMe()` from Kotlin to fetch the signed-in user's profile. Rust Inside builds the Android SDK from the sample Rust library and publishes it here as an AAR. The package includes the Kotlin bindings and native libraries for `arm64-v8a` and `x86_64`. It requires Android API level 23 or later. [Rust implementation](https://github.com/Rust-Inside/sample-rust-core/blob/v0.2.0/src/api.rs)

## Add the SDK with Gradle

Gradle can download the AAR and its Ivy descriptor directly from GitHub Releases. Add the following Ivy repository to `dependencyResolutionManagement.repositories` in `settings.gradle.kts`. Include `mavenCentral()` in the same block for Kotlin, coroutines, and JNA. [Release v0.2.0](https://github.com/Rust-Inside/sample-android-sdk/releases/tag/v0.2.0)

```kotlin
ivy {
    url = uri("https://github.com/Rust-Inside/sample-android-sdk/releases/download")
    patternLayout {
        ivy("v[revision]/[module]-[revision].xml")
        artifact("v[revision]/[artifact]-[revision].[ext]")
    }
    metadataSources { ivyDescriptor() }
    content { includeModule("dev.rustinside.sample_rust_coresdk", "sample-rust-core-android") }
}
```

Add the dependency to your app module's `build.gradle.kts`.

```kotlin
dependencies {
    implementation("dev.rustinside.sample_rust_coresdk:sample-rust-core-android:0.2.0")
}
```

## Fetch a profile

Add `<uses-permission android:name="android.permission.INTERNET" />` to your app's `AndroidManifest.xml`. Call `getMe()` from a coroutine, passing your account server's origin and the user's access token to the client.

```kotlin
import dev.rustinside.sample_rust_coresdk.AccountClient

suspend fun profileName(apiOrigin: String, accessToken: String): String =
    AccountClient(apiOrigin, "Bearer $accessToken").use { account ->
        account.getMe().displayName
    }
```

The Rust library sends the bearer token with `GET /v1/me`. You can read `id`, `handle`, and `displayName` from the returned profile. If the request fails, `getMe()` throws a Kotlin exception.

## License

[MIT](LICENSE)
