# Set Up Your Environment

Now that you have taken care of all the preliminary issues, we can start building the app. Throughout this guide, you will continously modify this project to extend its functionality to cover a number of basic features.

{% hint style="warning" %}
Due to [a bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/7155) it is necessary to include the `post_install` hook in your Podfile. The `post_install` is different depending on if you use Google Maps or Mapbox Maps:

* [Google Maps `post_install`](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/wiki/Podfile-post\_install-v4).
* [Mapbox Maps `post_install`](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/wiki/Podfile-post\_install-Mapbox-v4).
{% endhint %}

### Create an Xcode Project[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#create-an-xcode-project) <a href="#create-an-xcode-project" id="create-an-xcode-project"></a>

We recommend using [Xcode](https://developer.apple.com/xcode/) for following along. For this guide we will be using Xcode 14.2. Note that an iOS mobile device is not required, as Xcode allows the use of a simulator.

We start off by creating an Xcode project using the App template:

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode_template.png" alt=""><figcaption></figcaption></figure>

For the project settings, you can call it anything you like, however ensure the following settings are set to follow along easier:

* Interface: Storyboard
* Language: Swift

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode-project_options.png" alt=""><figcaption></figcaption></figure>

You should now have a project folder with the following files:

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode-project_folder.png" alt=""><figcaption></figcaption></figure>

For the sake of simplicity we will only be operating on these pre-generated files throughout the guide.

### Installing the MapsIndoors SDK[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#installing-the-mapsindoors-sdk) <a href="#installing-the-mapsindoors-sdk" id="installing-the-mapsindoors-sdk"></a>

MapsIndoors can be installed using CocoaPods ([Getting Started with CocoaPods](https://guides.cocoapods.org/using/getting-started.html)).

* Cocoapods
* Manually for Google Maps

#### Installing MapsIndoors Using CocoaPods[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#installing-mapsindoors-using-cocoapods) <a href="#installing-mapsindoors-using-cocoapods" id="installing-mapsindoors-using-cocoapods"></a>

1. Create a new `Podfile` in your project directory (same folder as your _.xcodeproj_) by running `pod init <XCODEPROJECTNAME>` in Terminal.
2.  Add your dependecies to the `Podfile` as follows (replace `YOUR_APPLICATION_TARGET_NAME_HERE` with your actual target name),

    ```properties
    source 'https://github.com/CocoaPods/Specs.git'

    platform :ios, '15.0' # Replace 15.0 with you iOS Minimum Deployment Target

    target 'YOUR_APPLICATION_TARGET_NAME_HERE' do
      use_frameworks!

      pod 'MapsIndoors', '~>4.1'
      # and your Map Specific pod
      # pod MapsIndoorsGoogleMaps
      # or
      # pod MapsIndoorsMapbox
    end
    ```
3. Add the `post_install` [for Google Maps](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/wiki/Podfile-post\_install-v4) or [for Mapbox Maps](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/wiki/Podfile-post\_install-Mapbox-v4) to the end of the `Podfile`.

{% hint style="warning" %}
In the line containing `pod 'MapsIndoors', '~>4.0'`, where it currently says `4.0`, be sure to replace this number with whatever the latest version of the iOS SDK is.
{% endhint %}

4. Save the `Podfile` and close Xcode.
5. Open a terminal in the directory of the project. `cd <path-to-project>`
6. Run `pod install` in the terminal.
7. From this time onwards, use the _.xcworkspace_ file to open the project.

{% hint style="info" %}
This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endhint %}

### Adding API Credentials[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#adding-api-credentials) <a href="#adding-api-credentials" id="adding-api-credentials"></a>

Open back up the project and navigate to the file `AppDelegate.swift`.

{% tabs %}
{% tab title="Google Maps" %}
1. Add the following import statements to the top of the file:

```swift
import GoogleMaps  
import MapsIndoorsCore
```

2. Insert the following into the `application(_:didFinishLaunchingWithOptions:)` method. If you are using `Mapbox` then provide your API Key when you add your map to the view inside `viewDidLoad()` in your `ViewController.swift`:

```swift
 GMSServices.provideAPIKey(`YOUR_GOOGLE_API_KEY`)
```

Finally, remember to replace `YOUR_GOOGLE_API_KEY` or `YOUR_MAPBOX_API_KEY` with your Google API key and `YOUR_MAPSINDOORS_API_KEY` with your MapsIndoors API demo key `d876ff0e60bb430b8fabb145`.
{% endtab %}

{% tab title="Mapbox" %}
1. Add the following import statements to the top of the file:

```swift
import Mapbox  
import MapsIndoorsCore
```

2. Insert the following into the `application(_:didFinishLaunchingWithOptions:)` method. If you are using `Mapbox` then provide your API Key when you add your map to the view inside `viewDidLoad()` in your `ViewController.swift`:

```swift
 ResourceOptions(accessToken: `YOUR_MAPBOX_API_KEY`)
 let initOptions = MapInitOptions(resourceOptions: myResourceOptions, styleURI: StyleURI.light)
      self.mapView = MapView(frame: view.bounds, mapInitOptions: initOptions)
      // set the MPMapConfig
      MPMapConfig(mapBoxView: mapView!, accessToken: "---")
      Task {
      await MPMapsIndoors.shared.load(apiKey:`YOUR_MAPSINDOORS_API_KEY`)
  }
```

Finally, remember to replace `YOUR_GOOGLE_API_KEY` or `YOUR_MAPBOX_API_KEY` with your Google API key and `YOUR_MAPSINDOORS_API_KEY` with your MapsIndoors API demo key `d876ff0e60bb430b8fabb145`.
{% endtab %}
{% endtabs %}
