# Create a New Project

You begin by creating an initial application. Throughout this tutorial, you will modify and extend that starter application to create a simple application which covers the basic features of this guide.

### Set Up Your Environment[​](https://docs.mapsindoors.com/getting-started/android/v4/new-project#set-up-your-environment) <a href="#set-up-your-environment" id="set-up-your-environment"></a>

This guide explains how to start using a MapsIndoors map in your Android application using the MapsIndoors Android SDK v4.

We recommend using Android Studio for using this tutorial. Read how to set it up here: [Installing Android Studio](https://developer.android.com/studio/install)

If you do not have a Android device, you can [set up an emulator through Android Studio](https://developer.android.com/studio/run/emulator).

If you already have an Android device, make sure to [enable developer mode and USB debugging](https://developer.android.com/studio/debug/dev-options#enable)

To benefit from the guides, you will need basic knowledge about:

* Android Development
* Google Maps Android API

You can get started in two ways, either by reviewing and modifying the [basic example](https://docs.mapsindoors.com/getting-started/android/v4/new-project#basic-example) or do the [clean setup](https://docs.mapsindoors.com/getting-started/android/v4/new-project#setup-mapsindoors). The clean setup is only written for Google Maps, and we recommend following the [basic example](https://docs.mapsindoors.com/getting-started/android/v4/new-project#basic-example).

### Basic Example[​](https://docs.mapsindoors.com/getting-started/android/v4/new-project#basic-example) <a href="#basic-example" id="basic-example"></a>

The tutorial will be based on you starting from our basic map implementation. This contains basic UI implementations together with layout files and drawables used to create the UI. You will then be guided through how to implement the MapsIndoors SDK into this app.

The basic example contains a single `activity` app with already made `fragments` to host the different logic to get a complete app interacting with a map and `MapsIndoors` data.

You can find the basic example for Google Maps here: [Java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google\_Maps/mapsindoorsgettingstartedbasicjava) or [Kotlin](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google\_Maps/mapsindoorsgettingstartedbasickotlin)

The Mapbox basic example is located here: [Java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapBox/mapsindoorsgettingstartedbasicjava) or [Kotlin](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapBox/mapsindoorsgettingstartedbasickotlin)

You can open the project through Android Studio by navigating through **File -> New -> Project from Version Control -> GitHub**. Log in and clone the project.

You can also follow the steps below to start your app from scratch or to enhance the Basic Examples, more features will be explained in later guides.

### Setup MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/new-project#setup-mapsindoors) <a href="#setup-mapsindoors" id="setup-mapsindoors"></a>

If you don't already have a project, we recommend using the Google Maps Activity preset from Android Studio to getting started on developing your MapsIndoors project. You find the Google Maps Activity project through **File -> New -> New Project... -> Google Maps Activity**.

Add the MapsIndoors SDK as a dependency to your project. The _AAR_ for the MapsIndoors SDK contains both Java classes, SDK resources and an `AndroidManifest.xml` template which gets merged into your application's `AndroidManifest.xml` during build process.

Add or merge in the following to your app's build gradle file (usually called `build.gradle`).

Make sure that the minimum Android SDK version is 21 (aka. "Android Lollipop", version 5.0) or above:

{% hint style="info" %}
Please note that mapsindoors uses java 8 language features, that requires desugaring if `minSdkVersion` is 24 or below. Read how to enable this in gradle here: [Use Java 8 language features and APIs](https://developer.android.com/studio/write/java8-support)
{% endhint %}

```
android {
    defaultConfig {
        minSdkVersion 21
    }
    ...
}
```

MapsIndoors relies on Java 8 features, so you must add the following compile options, also in _android_ section of your _build.gradle_ file:

```
android {
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Add the following dependencies and the MapsIndoors maven repository:

`Gson` and `okhttp` is used by MapsIndoors to function properly with network calls and deserializing.



{% tabs %}
{% tab title="Google Maps" %}


`play-services-maps` is used for Google Maps which MapsIndoors is build on top of on Android.

```
dependencies {
    ...
    implementation 'com.google.android.gms:play-services-maps:17.0.0'
    implementation 'com.google.code.gson:gson:2.8.6'
    implementation 'com.mapspeople.mapsindoors:googlemapssdk:4.1.0'
    implementation 'com.squareup.okhttp3:okhttp:4.9.0'
}
repositories{
    maven {
        url 'https://maven.mapsindoors.com/'
    }
}
```

Put those lines in your proguard-rules files:

```
-keep interface com.mapsindoors.core.** { *; }
-keep class com.mapsindoors.core.errors.** { *; }
-keepclassmembers class com.mapsindoors.core.models.** { <fields>; }
-keep class com.mapsindoors.core.MPDebugLog
```

Sync your project with gradle.

> This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endtab %}

{% tab title="Mapbox" %}


```
dependencies {
    ...
    implementation ('com.mapbox.maps:android:10.8.0'){
        exclude group: 'group_name', module: 'module_name'
    }
    implementation 'com.google.code.gson:gson:2.8.6'
    implementation 'com.mapspeople.mapsindoors:mapboxsdk:4.1.0'
    implementation 'com.squareup.okhttp3:okhttp:4.9.0'
}
repositories{
    maven {
        url 'https://maven.mapsindoors.com/'
    }
}
```

Put those lines in your proguard-rules files:

```
-keep interface com.mapsindoors.core.** { *; }
-keep class com.mapsindoors.core.errors.** { *; }
-keepclassmembers class com.mapsindoors.core.models.** { <fields>; }
-keep class com.mapsindoors.core.MPDebugLog
```

Sync your project with gradle.

> This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endtab %}
{% endtabs %}
