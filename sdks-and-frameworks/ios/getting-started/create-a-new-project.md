# Set Up Your Environment

Now that you have taken care of all the preliminary issues, we can start building the app. Throughout this guide, you will continously modify this project to extend its functionality to cover a number of basic features.

### Create an Xcode Project[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#create-an-xcode-project) <a href="#create-an-xcode-project" id="create-an-xcode-project"></a>

We recommend using [Xcode](https://developer.apple.com/xcode/) for following along. For this guide we will be using Xcode 15.4. Note that an iOS mobile device is not required, as Xcode allows the use of a simulator.

We start off by creating an Xcode project using the App template:

<figure><img src="../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

For the project settings, you can call it anything you like, however ensure the following settings are set to follow along easier:

* Interface: Storyboard
* Language: Swift

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

You should now have a project folder with the following files:

<figure><img src="../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

For the sake of simplicity we will only be operating on these pre-generated files throughout the guide.

### Installing the MapsIndoors SDK[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#installing-the-mapsindoors-sdk) <a href="#installing-the-mapsindoors-sdk" id="installing-the-mapsindoors-sdk"></a>

MapsIndoors can be installed using Swift Package Manger, CocoaPods or you can install the XCFrameworks manually if using Google Maps.

{% tabs %}
{% tab title="Swift Package Manager" %}
1. Open your Xcode project or workspace, then go to **File** > **Add Packages Dependencies...**.
2.  In the search bar in the top right corner, enter\
    [`https://github.com/MapsPeople/mapsindoors-mapbox-ios.git`](https://github.com/MapsPeople/mapsindoors-mapbox-ios.git) (to use Mapbox Maps)

    or

    [`https://github.com/MapsPeople/mapsindoors-googlemaps-ios.git`](https://github.com/MapsPeople/mapsindoors-googlemaps-ios.git) (to use Google Maps)
3. Select the _Dependency Rule_ you want to apply to the MapsIndoors SDK
   * A common choice for _Dependency Rule_ is "Up to Next Major Version", specifying `4.6.0` as the minimum version. To instead install a specific version set the _Dependency Rule_ field to "Exact Version" and insert the desired version. The latest version of the MapsIndoors SDK is `4.6.2`.
4. Hit enter or Click **Add Package**.
5. In the new window select the `MapsIndoorsMapbox` or `MapsIndoorsGoogleMaps` library and click **Add Package**. Once SPM finishes installing the SDK you will see 3 new dependencies under **Package Dependencies**: `MapsIndoors`, `MapsIndoorsCore`, and `MapsIndoorsMapbox` or `MapsIndoorsGoogleMaps` (plus their respective dependencies).
6. Click on your project's target, scroll down to `Frameworks, Libraries, and Embedded Content` and click the plus button.
7. From the list select `MapsIndoorsMapbox` or `MapsIndoorsGoogleMaps` and click add.

Now you can use start using MapsIndoors in your project by using `import MapsIndoors` in your source code.

{% hint style="warning" %}
If you're using Mapbox, make sure to configure your secret token in order to download the required dependencies from Mapbox when installing the Swift Package: [https://docs.mapbox.com/ios/maps/guides/install/#configure-credentials](https://docs.mapbox.com/ios/maps/guides/install/#configure-credentials)
{% endhint %}
{% endtab %}

{% tab title="Cocoapods" %}
1. Create a new `Podfile` in your project directory (same folder as your _.xcodeproj_) by running `pod init <XCODEPROJECTNAME>` in Terminal.
2.  Add your dependecies to the `Podfile` as follows (replace `YOUR_APPLICATION_TARGET_NAME_HERE` with your actual target name),

    ```ruby
    source 'https://github.com/CocoaPods/Specs.git'

    platform :ios, '15.0' # Replace 15.0 with you iOS Minimum Deployment Target

    target 'YOUR_APPLICATION_TARGET_NAME_HERE' do
      # Remove the comment mark to use your map specific MapsIndoors pod
      # pod 'MapsIndoorsGoogleMaps', '~> 4.6'
      # or
      # pod 'MapsIndoorsMapbox11', '~> 4.6'
    end
    ```

{% hint style="warning" %}
If you're using Mapbox, make sure to configure your secret token in order to download the required dependencies from Mapbox when running `pod install`: [https://docs.mapbox.com/ios/maps/guides/install/#configure-credentials](https://docs.mapbox.com/ios/maps/guides/install/#configure-credentials)
{% endhint %}

{% hint style="danger" %}
If you are upgrading from a version previous to 4.6.0 you must remove the `post_install` script parts previously required by MapsIndoors [for Google Maps](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/wiki/Podfile-post_install-v4).
{% endhint %}

4. Save the `Podfile` and close Xcode.
5. Open a terminal in the directory of the project. `cd <path-to-project>`
6. Run `pod install` in the terminal.
7. From this time onwards, use the _.xcworkspace_ file to open the project.
{% endtab %}

{% tab title="Manually for Google Maps" %}
* Download the [Google Maps iOS SDK 8.4.0](https://dl.google.com/dl/cpdc/bad57bdf1f06c544/GoogleMaps-8.4.0.tar.gz)
* Copy the following frameworks to the root folder of your app project (in Finder, _<mark style="color:red;">**not**</mark>_ in Xcode)
  * GoogleMaps-8.4.0/Base/Frameworks/GoogleMapsBase.framework
  * GoogleMaps-8.4.0/Maps/Frameworks/GoogleMaps.framework
  * GoogleMaps-8.4.0/Maps/Frameworks/GoogleMapsCore.framework
* In Xcode, drag the `GoogleMaps.bundle` from the `GoogleMaps-8.4.0/Maps/Resources/GoogleMapsResources` folder into the top level directory of your Xcode project. When prompted, ensure **"**&#x43;opy items if needed" **is** selected.
* Download and unzip the following MapsIndoors SDK v4 XCFrameworks from the [latest release](https://github.com/MapsPeople/MapsIndoors-SDK-iOS/releases):
  * MapsIndoors
  * MapsIndoorsCore
  * MapsIndoorsGoogleMaps
* Drag and drop the three MapsIndoors XCFrameworks into your Xcode project. In the dialog that pops up, choose “Copy items if needed” and make sure the XCFramework is added to your app target.
* In Xcode, go to "General" and expand "Frameworks, Libraries, and Embedded Content" and make sure the three MapsIndoors XCFrameworks are marked as "Embed & Sign".
* In Xcode, go to "Build Settings" for your target and make sure the following entries are present in the `FRAMEWORK_SEARCH_PATHS`

```
$(inherited)
$(PROJECT_DIR)/**
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endhint %}

### Adding API Credentials[​](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#adding-api-credentials) <a href="#adding-api-credentials" id="adding-api-credentials"></a>

Open back up the project and navigate to the file `AppDelegate.swift`.

{% tabs %}
{% tab title="Mapbox" %}
1. Add the following import statements to the top of the file:

```swift
import MapboxMaps  
import MapsIndoorsCore
```

2. Insert the following into the `application(_:didFinishLaunchingWithOptions:)` method. If you are using `Mapbox` then provide your API Key when you add your map to the view inside `viewDidLoad()` in your `ViewController.swift`:

```swift
ResourceOptions(accessToken: "YOUR_MAPBOX_API_KEY")
let initOptions = MapInitOptions(resourceOptions: myResourceOptions, styleURI: StyleURI.light)
self.mapView = MapView(frame: view.bounds, mapInitOptions: initOptions)
// Set the MPMapConfig
MPMapConfig(mapBoxView: mapView!, accessToken: "---")
Task {
    await MPMapsIndoors.shared.load(apiKey:"YOUR_MAPSINDOORS_API_KEY")
}
```

Finally, remember to replace `YOUR_MAPBOX_API_KEY` with your Mapbox Access Token and `YOUR_MAPSINDOORS_API_KEY` with your MapsIndoors API key (or use the demo key `d876ff0e60bb430b8fabb145)`.
{% endtab %}

{% tab title="Google Maps" %}
1. Add the following import statements to the top of the file:

```swift
import GoogleMaps  
import MapsIndoorsCore
```

2. Insert the following into the `application(_:didFinishLaunchingWithOptions:)` method. If you are using `Mapbox` then provide your API Key when you add your map to the view inside `viewDidLoad()` in your `ViewController.swift`:

```swift
GMSServices.provideAPIKey("YOUR_GOOGLE_API_KEY")
```

Finally, remember to replace `YOUR_GOOGLE_API_KEY`  with your Google API key.
{% endtab %}
{% endtabs %}
