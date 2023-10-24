# Set Up Your Environment

Now that you have taken care of all the preliminary issues, we can start building the app. Throughout this guide, you will continously modify this project to extend its functionality to cover a number of basic features.

{% hint style="warning" %}
**KNOWN IOS ISSUES**

1. Developing on the new Arm-based Apple Silicon (M1) Macs requires building and running on a physical iOS device or using an iOS simulator running iOS 13.7, e.g. iPhone 11. This is a temporary limitation in Google Maps SDK for iOS, and as such also a limitation in MapsIndoors, due to the dependency to Google Maps.
2. Due to [a bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/7155) it is necessary to include the `post_install` hook in your Podfile described in the [PodFile post\_install](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install) wiki.
{% endhint %}



### Create an Xcode Project[​](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment#create-an-xcode-project) <a href="#create-an-xcode-project" id="create-an-xcode-project"></a>

We recommend using [Xcode](https://developer.apple.com/xcode/) for following along, for this guide we will be using Xcode 13.0. Note that an iOS mobile device is not required, as Xcode allows the use of a simulator. Furthermore, in accordance with the known issues with Google Maps and Arm-based Apple Silicon (M1) Macs, we will be using an iPhone 11 (iOS 13.7) simulator throughout.

We start off by creating an Xcode project using the App template,

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode_template.png" alt=""><figcaption></figcaption></figure>

For the project settings, you can call it anything you like, however ensure the following settings are set to follow along easier,

* Interface: Storyboard
* Language: Swift

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode-project_options.png" alt=""><figcaption></figcaption></figure>

You should now have a project folder with the following files,

<figure><img src="https://docs.mapsindoors.com/img/getting-started/ios-xcode-project_folder.png" alt=""><figcaption></figcaption></figure>

For the sake of simplicity we will only be operating on these pre-generated files throughout the guide.

### Installing the MapsIndoors SDK[​](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment#installing-the-mapsindoors-sdk) <a href="#installing-the-mapsindoors-sdk" id="installing-the-mapsindoors-sdk"></a>

MapsIndoors can either be installed using CocoaPods ([Getting Started with CocoaPods](https://guides.cocoapods.org/using/getting-started.html)) or through a manual installation.

{% tabs %}
{% tab title="Cocoapods" %}
#### Installing MapsIndoors Using CocoaPods[​](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment#installing-mapsindoors-using-cocoapods) <a href="#installing-mapsindoors-using-cocoapods" id="installing-mapsindoors-using-cocoapods"></a>

From MapsIndoors SDK version 3.32.0 and up, in order for CocoaPods to fetch the SDK properly it is necessary to install `git-lfs` ([Install Guide](https://git-lfs.github.com/)).

1. Create an empty text file named `Podfile` in your project directory (same folder as your _.xcodeproj_).
2.  Add your dependencies to the `Podfile` as followed (replace `YOUR_APPLICATION_TARGET_NAME_HERE` with your project name),

    ```
    source 'https://github.com/CocoaPods/Specs.git'

    platform :ios, '15.0' # Replace 15.0 with you iOS Minimum Deployment Target

    target 'YOUR_APPLICATION_TARGET_NAME_HERE' do
      use_frameworks!

      pod 'MapsIndoors', '~>3.50'
    end
    ```
3. Add the [`post_install`](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install) to the end of the `Podfile`. <mark style="background-color:yellow;">(In the line containing</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">`pod 'MapsIndoors', '~>3.50'`</mark><mark style="background-color:yellow;">, where it currently says</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">`3.50`</mark><mark style="background-color:yellow;">, be sure to replace this number with whatever the latest version of the iOS SDK is.)</mark>
4. Save the `Podfile` and close Xcode.
5. Open a terminal in the directory of the project. `cd \<path-to-project>`
6. Run `pod install` in the terminal.
7. From this time onwards, use the _.xcworkspace_ file to open the project.
{% endtab %}

{% tab title=" Manually" %}
#### Install MapsIndoors Manually[​](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment#install-mapsindoors-manually) <a href="#install-mapsindoors-manually" id="install-mapsindoors-manually"></a>

* Download the [Google Maps iOS SDK 4.2.0](https://dl.google.com/dl/cpdc/870a9df85dbcbadc/GoogleMaps-4.2.0.tar.gz)
* Copy the following frameworks to the folder of your app project (in Finder, **not** in Xcode)
  * GoogleMaps-4.2.0/Base/Frameworks/GoogleMapsBase.framework
  * GoogleMaps-4.2.0/Maps/Frameworks/GoogleMaps.framework
  * GoogleMaps-4.2.0/Maps/Frameworks/GoogleMapsCore.framework
* Drag the `GoogleMaps.bundle` from the `GoogleMaps.framework/Resources` folder into the top level directory of your Xcode project. When prompted, ensure **Copy items into destination group's folder** is _**not**_ selected.
* Download and unzip the latest [v3 MapsIndoors.xcframework](https://github.com/MapsIndoors/MapsIndoorsIOS/releases/download/3.50.1/MapsIndoors.xcframework.zip)
* Drag and drop the MapsInbdoors XCFramework into your XCode project. In the dialog that pops up, choose “Copy items if needed” and make sure the XCFramework is added to the correct target
* In Xcode, go to "General" and expand "Frameworks, Libraries, and Embedded Content" and make sure the MapsIndoors.xcframework is marked as "Embed & Sign"
* In Xcode, go to "Build Settings" for your target and make sure the following entries are present in the `FRAMEWORK_SEARCH_PATHS`
  * `$(inherited)`
  * `$(PROJECT_DIR)/**`
{% endtab %}
{% endtabs %}

{% hint style="info" %}
This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
{% endhint %}

### Adding API Credentials[​](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment#adding-api-credentials) <a href="#adding-api-credentials" id="adding-api-credentials"></a>

Open back up the project and navigate to the file `AppDelegate.swift`.

1.  Add the following import statements to the top of the file,

    ```
    import GoogleMaps  
    import MapsIndoors
    ```
2. Insert the following into the `application(_:didFinishLaunchingWithOptions:)` method,

```
GMSServices.provideAPIKey("YOUR_GOOGLE_API_KEY")  
MapsIndoors.provideAPIKey("YOUR_MAPSINDOORS_API_KEY",  
            googleAPIKey:"YOUR_GOOGLE_API_KEY")
```

Finally, remember to replace `YOUR_GOOGLE_API_KEY` with your Google API key and `YOUR_MAPSINDOORS_API_KEY` with your MapsIndoors API demo key `d876ff0e60bb430b8fabb145`.
