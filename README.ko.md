# Sample Rust Core · Android SDK

[English](README.md) | [한국어](README.ko.md)

Kotlin에서 `account.getMe()`를 호출하면 로그인한 사용자의 프로필을 받습니다. Rust Inside는 샘플 Rust 라이브러리로 Android SDK를 빌드해 이 저장소에 AAR로 배포합니다. AAR에는 Kotlin 바인딩과 `arm64-v8a`·`x86_64`용 네이티브 라이브러리가 들어 있습니다. Android API 23 이상을 지원합니다. [Rust 구현](https://github.com/Rust-Inside/sample-rust-core/blob/v0.2.0/src/api.rs)

## Gradle에 추가하기

Gradle이 GitHub Releases에서 AAR과 Ivy 메타데이터를 직접 받습니다. `settings.gradle.kts`의 `dependencyResolutionManagement.repositories`에 아래 Ivy 저장소를 추가하세요. 같은 블록에 `mavenCentral()`도 있어야 Kotlin, 코루틴, JNA 의존성을 받을 수 있습니다. [v0.2.0 릴리스](https://github.com/Rust-Inside/sample-android-sdk/releases/tag/v0.2.0)

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

앱 모듈의 `build.gradle.kts`에 의존성을 추가합니다.

```kotlin
dependencies {
    implementation("dev.rustinside.sample_rust_coresdk:sample-rust-core-android:0.2.0")
}
```

## 프로필 조회

앱의 `AndroidManifest.xml`에 `<uses-permission android:name="android.permission.INTERNET" />`를 추가하세요. 클라이언트에 계정 서버 주소와 사용자의 액세스 토큰을 넘기고, 코루틴에서 `getMe()`를 호출합니다.

```kotlin
import dev.rustinside.sample_rust_coresdk.AccountClient

suspend fun profileName(apiOrigin: String, accessToken: String): String =
    AccountClient(apiOrigin, "Bearer $accessToken").use { account ->
        account.getMe().displayName
    }
```

Rust 라이브러리가 인증 헤더를 붙여 `GET /v1/me`를 요청합니다. 응답에서 `id`, `handle`, `displayName`을 읽을 수 있고, 요청이 실패하면 `getMe()`가 Kotlin 예외를 던집니다.

## 라이선스

[MIT](LICENSE)
