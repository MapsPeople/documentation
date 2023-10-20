# Create a New Project

You begin by creating an initial application. Throughout this tutorial, you will modify and extend that starter application to create a simple application which covers the basic features of this guide.

[If you need help to create a new project use this official Flutter guide](https://docs.flutter.dev/get-started/test-drive?tab=terminal)

### Set Up Your Environment[​](https://docs.mapsindoors.com/getting-started/flutter/new-project#set-up-your-environment) <a href="#set-up-your-environment" id="set-up-your-environment"></a>

This guide explains how to start using a MapsIndoors map in your Flutter application using the MapsIndoors Flutter SDK.

You will need to [download and install](https://docs.flutter.dev/get-started/install) the flutter toolchain.

We recommend using Android Studio and XCode for using this tutorial. Read how to set Android Studio up here: [Installing Android Studio](https://developer.android.com/studio/install)

If you do not have any devices, you can [set up an emulator through Android Studio](https://developer.android.com/studio/run/emulator), or in Xcode.

If you already have an Android device, make sure to [enable developer mode and USB debugging](https://developer.android.com/studio/debug/dev-options#enable)

To benefit from the guides, you will need basic knowledge about:

* Flutter Development
* Mobile Development

### Set Up Your Project[​](https://docs.mapsindoors.com/getting-started/flutter/new-project#set-up-your-project) <a href="#set-up-your-project" id="set-up-your-project"></a>

To get your project started follow these steps:

1. Create a new project by writing the following command in your terminal

`flutter create --platforms ios,android getting_started_mapsindoors_flutter`

This will create a new project targeting Android and iOS platforms.

2. Go to `lib/main.dart` and delete its contents, we will fill it up again during this guide.

If you want to see an example of how this guide will look when completed, you can [find it on Github here](https://github.com/MapsPeople/getting\_started\_flutter).

### Set Up MapsIndoors[​](https://docs.mapsindoors.com/getting-started/flutter/new-project#set-up-mapsindoors) <a href="#set-up-mapsindoors" id="set-up-mapsindoors"></a>

#### Google Maps[​](https://docs.mapsindoors.com/getting-started/flutter/new-project#google-maps) <a href="#google-maps" id="google-maps"></a>

To get started with your project add MapsIndoors version `2.0.0` to your `pubspec.yaml`.

```yaml
mapsindoors_googlemaps: ^2.0.0
```





{% tabs %}
{% tab title="Android" %}


First you need to extend the allowed gradle repositories to allow the SDK to resolve correctly:

1. Navigate to the app's project level `build.gradle`.
2. add `maven { url 'https://maven.mapsindoors.com/' }` to `allprojects`/`repositories` after `mavenCentral()`

```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://maven.mapsindoors.com/' }
    }
}
```

To get the underlying Google Map to work, you need to perform the following steps:

1. Navigate to `android/app/src/main/res/value`.
2. Create a file in this folder called `google_maps_api_key.xml`.
3. Copy and paste the below code snippet and replace `YOUR_KEY_HERE` with your Google Maps API key.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="google_maps_key">YOUR_KEY_HERE</string>
</resources>
```
{% endtab %}

{% tab title="iOS" %}
To get the underlying Google Map to function, you need to perform the following steps:

**Providing API key**[**​**](https://docs.mapsindoors.com/getting-started/flutter/new-project#providing-api-key)

**Swift**[**​**](https://docs.mapsindoors.com/getting-started/flutter/new-project#swift)

1. Navigate to `iOS/Runner/AppDelegate.swift`.
2. Import GoogleMaps on the class
3. Add this code as the first line inside the application function: `GMSServices.provideAPIKey("YOUR GOOGLE MAPS API KEY HERE")`

**Objective-C**[**​**](https://docs.mapsindoors.com/getting-started/flutter/new-project#objective-c)

1. Navigate to `iOS/Runner/AppDelegate.h`.
2. Import `#import "GoogleMaps/GoogleMaps.h"` on the class.
3. Add this code as the first line inside the application function: `[GMSServices provideAPIKey:@"YOUR GOOGLE MAPS API KEY HERE"];`

**Adding MapsIndoors script specific to Google Maps, to Podfile**[**​**](https://docs.mapsindoors.com/getting-started/flutter/new-project#adding-mapsindoors-script-specific-to-google-maps-to-podfile)

After this you should navigate into the iOS folder of your flutter project and add this script to the applications Podfile: [MapsIndoors podfile Post install](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install-v4)
{% endtab %}
{% endtabs %}

#### Mapbox[​](https://docs.mapsindoors.com/getting-started/flutter/new-project#mapbox) <a href="#mapbox" id="mapbox"></a>

To get started with your project add MapsIndoors version `2.0.0` to your `pubspec.yaml`.

```yaml
mapsindoors_mapbox: ^2.0.0
```



{% tabs %}
{% tab title="Android" %}


First you need to extend the allowed gradle repositories to allow the SDK to resolve correctly:

1. Navigate to the app's project level `build.gradle`.
2. add `maven { url 'https://maven.mapsindoors.com/' }` to `allprojects`/`repositories` after `mavenCentral()`
3. add mapbox to repositories like in the example below:

```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://maven.mapsindoors.com/' }
    }
    maven {
        url 'https://api.mapbox.com/downloads/v2/releases/maven'
        authentication {
            basic(BasicAuthentication)
        }
        credentials {
            // Do not change the username below.
            // This should always be `mapbox` (not your username).
            username = "mapbox"
            // Use the secret token you stored in gradle.properties as the password
            password = project.properties['MAPBOX_DOWNLOADS_TOKEN'] ?: ""
        }
    }
}
```

4. add your mapbox download token to gradle.properties

```properties
MAPBOX_DOWNLOADS_TOKEN=YOUR_DOWNLOAD_TOKEN
```

To get the underlying Mapbox Map to work, you need to perform the following steps:

1. Navigate to `android/app/src/main/res/value`.
2. Create a file in this folder called `mapbox_api_key.xml`.
3. Copy and paste the below code snippet and replace `YOUR_KEY_HERE` with your Mapbox keys.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="mapbox_api_key" translatable="false">YOUR_KEY_HERE</string>
    <string name="mapbox_access_token" translatable="false">YOUR_KEY_HERE</string>
</resources>
```

{% endtab %}

{% tab title="iOS" %}
The MapsIndoors SDK requires iOS 13, so make sure that your podfile is configured for iOS 13. Add !use\_frameworks inside your app target as well.

```properties
platform :ios, '13.0

target 'MyApp' do
  use_frameworks!

post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
    end
  ...
end
...
```

**Providing API key**[**​**](https://docs.mapsindoors.com/getting-started/flutter/new-project#providing-api-key-1)

Navigate to your application settings in XCode and add your Mapbox public access token to info with the key MBXAccessToken Setup your secret access token for downloading the sdk. Read how to do this here: [Configure credentials](https://docs.mapbox.com/ios/maps/guides/install/#configure-credentials)
{% endtab %}
{% endtabs %}

