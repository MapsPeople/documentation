# Create a New Project

You begin by creating an initial application. Throughout this tutorial, you will modify and extend that starter application to create a simple application which covers the basic features of this guide.

### Set Up Your Environment​ <a href="#set-up-your-environment" id="set-up-your-environment"></a>

This guide explains how to start using a MapsIndoors map in your Android application using the MapsIndoors Android SDK v5.

We recommend using Android Studio for using this tutorial. Read how to set it up here: [Installing Android Studio](https://developer.android.com/studio/install)

If you do not have a Android device, you can [set up an emulator through Android Studio](https://developer.android.com/studio/run/emulator).

If you already have an Android device, make sure to [enable developer mode and USB debugging](https://developer.android.com/studio/debug/dev-options#enable)

To benefit from the guides, you will need basic knowledge about:

* Android development with Kotlin, including coroutines
* The Google Maps SDK for Android or the Mapbox Maps SDK for Android, depending on the map provider you use

You can get started in two ways, either by reviewing and modifying the [basic example](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google_Maps/mapsindoorsgettingstartedbasickotlin) or by doing the [clean setup](create-a-new-project.md#setup-mapsindoors). The clean setup covers both Google Maps and Mapbox.

{% hint style="warning" %}
The basic examples have not been updated to MapsIndoors SDK v5 yet; they still use v4. Until they are, use the [clean setup](create-a-new-project.md#setup-mapsindoors) to follow this guide with v5.
{% endhint %}

### Basic Example​ <a href="#basic-example" id="basic-example"></a>

The tutorial will be based on you starting from our basic map implementation. This contains basic UI implementations together with layout files and drawables used to create the UI. You will then be guided through how to implement the MapsIndoors SDK into this app.

The basic example contains a single `activity` app with already made `fragments` to host the different logic to get a complete app interacting with a map and `MapsIndoors` data.

You can find the basic example for Google Maps here: [Kotlin](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google_Maps/mapsindoorsgettingstartedbasickotlin)

The Mapbox basic example is located here: [Kotlin](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapBox/mapsindoorsgettingstartedbasickotlin)

You can open the project through Android Studio by navigating through **File -> New -> Project from Version Control -> GitHub**. Log in and clone the project.

You can also follow the steps below to start your app from scratch. More features will be explained in later guides.

### Setup MapsIndoors​ <a href="#setup-mapsindoors" id="setup-mapsindoors"></a>

If you don't already have a project, create one in Android Studio through **File -> New -> New Project... -> Empty Activity**, and choose Kotlin as the language and Kotlin DSL as the build configuration language. For Google Maps, you can instead use the Google Maps Views Activity template; see [Create a Google Maps project in Android Studio](https://developers.google.com/maps/documentation/android-sdk/start#create-project).

MapsIndoors SDK v5 has the following build requirements:

* Android Gradle Plugin 9.2.1 or newer, which builds Kotlin with Kotlin 2.2 or newer
* JDK 17
* `compileSdk` 37
* `minSdk` 24 (Android 7.0) or above

The guide uses Gradle's [version catalog](https://developer.android.com/build/migrate-to-catalogs) (`gradle/libs.versions.toml`) and Kotlin DSL build files, which is what new Android Studio projects use.

In the `android` section of your app module's build file (usually `app/build.gradle.kts`), set the SDK versions and compile with Java 17. The MapsIndoors SDK is compiled to Java 17 bytecode, so your app must target Java 17 as well:

```kotlin
android {
    compileSdk = 37

    defaultConfig {
        minSdk = 24
        targetSdk = 37
    }

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
}
```

Next, add the MapsIndoors SDK and the map provider's SDK as dependencies, and add the MapsIndoors Maven repository. The MapsIndoors SDK brings its own dependencies, such as Gson and OkHttp, so you do not need to add them yourself. The guide also uses `lifecycleScope` from `lifecycle-runtime-ktx` to call the SDK's `suspend` functions.

{% tabs %}
{% tab title="Google Maps" %}
`play-services-maps` is the Google Maps SDK which MapsIndoors is built on top of on Android.

Add the following to `gradle/libs.versions.toml`:

```toml
[versions]
mapsindoors = "5.0.0"
play-services-maps = "19.0.0"
lifecycle = "2.10.0"

[libraries]
mapsindoors-googlemaps = { module = "com.mapspeople.mapsindoors:googlemaps", version.ref = "mapsindoors" }
play-services-maps = { module = "com.google.android.gms:play-services-maps", version.ref = "play-services-maps" }
androidx-lifecycle-runtime-ktx = { module = "androidx.lifecycle:lifecycle-runtime-ktx", version.ref = "lifecycle" }
```

Add the MapsIndoors Maven repository to `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven("https://maven.mapsindoors.com/")
    }
}
```

Add the dependencies to `app/build.gradle.kts`:

```kotlin
dependencies {
    ...
    implementation(libs.mapsindoors.googlemaps)
    implementation(libs.play.services.maps)
    implementation(libs.androidx.lifecycle.runtime.ktx)
}
```

Sync your project with Gradle.

> This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endtab %}

{% tab title="Mapbox" %}
The MapsIndoors Mapbox SDK is built on the Mapbox Maps SDK v11 artifact with 16 KB page size support, `android-ndk27`. Use that artifact, at the version below, in your app as well; adding the plain `com.mapbox.maps:android` artifact next to it fails the build with duplicate classes.

Add the following to `gradle/libs.versions.toml`:

```toml
[versions]
mapsindoors = "5.0.0"
mapbox = "11.18.1"
lifecycle = "2.10.0"

[libraries]
mapsindoors-mapbox = { module = "com.mapspeople.mapsindoors:mapbox-v11", version.ref = "mapsindoors" }
mapbox-maps = { module = "com.mapbox.maps:android-ndk27", version.ref = "mapbox" }
androidx-lifecycle-runtime-ktx = { module = "androidx.lifecycle:lifecycle-runtime-ktx", version.ref = "lifecycle" }
```

The Mapbox Maven repository requires a secret Mapbox access token with the `Downloads:Read` scope, as described in [Prerequisites](prerequisites.md). Store it in `~/.gradle/gradle.properties` (not in your project, so it is not committed):

```properties
MAPBOX_DOWNLOADS_TOKEN=YOUR_SECRET_MAPBOX_ACCESS_TOKEN
```

Add the MapsIndoors and Mapbox Maven repositories to `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven("https://maven.mapsindoors.com/")
        maven {
            url = uri("https://api.mapbox.com/downloads/v2/releases/maven")
            authentication {
                create<BasicAuthentication>("basic")
            }
            credentials {
                // This should always be `mapbox` (not your username).
                username = "mapbox"
                password = providers.gradleProperty("MAPBOX_DOWNLOADS_TOKEN").get()
            }
        }
    }
}
```

Add the dependencies to `app/build.gradle.kts`:

```kotlin
dependencies {
    ...
    implementation(libs.mapsindoors.mapbox)
    implementation(libs.mapbox.maps)
    implementation(libs.androidx.lifecycle.runtime.ktx)
}
```

Sync your project with Gradle.

> This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endtab %}
{% endtabs %}

{% hint style="info" %}
You do not need to add any ProGuard or R8 rules for MapsIndoors. The SDK ships its own consumer rules, which are applied automatically when you enable minification.
{% endhint %}
