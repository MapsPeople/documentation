# Integrating MapsIndoors into your own App

The **MapsIndoors Template** is a downloadable starting point for you to integrate basic usage of MapsIndoors, containing search and directions functionalities, into your existing app. If you just want to get started with a simple solution with no customisation, this should fulfil your needs. Going through this guide will also teach you some principles on how MapsIndoors interacts with an app, and is a natural next step after the "Getting Started" guides.

If you need more customisation you can implementing your own solution using the documentation found on this site, or modify this code as needed.

**MapsIndoors Template** is provided as is, and can be integrated into your existing app. If you need further features, or want to customize existing ones, you're free to modify this one to your needs. However, MapsPeople offers no support or responsibility for changes made.

### Prerequisites[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#prerequisites) <a href="#prerequisites" id="prerequisites"></a>

Before you get started, you need to get the API keys needed. This process is the same for both platforms.

#### Get Your Google Maps API key[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#get-your-google-maps-api-key) <a href="#get-your-google-maps-api-key" id="get-your-google-maps-api-key"></a>

First, you need to [setup at a new project in the Google Cloud Console](https://developers.google.com/maps/gmp-get-started), just like you did in the ["Getting Started"](https://docs.mapsindoors.com/getting-started/ios/getting-started/ios) guide (**Please note:** You are going to need a Google Billing Account for this step, so go ahead and [create one](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account) if you haven't already). When the project is created, the following APIs and the specific SDK you plan to use must be enabled from the [Maps API Library Page](https://console.cloud.google.com/apis/library?filter=category:maps).

* Google Maps Distance Matrix API
* Google Maps Directions API
* Google Places API Web Service
* Maps SDK for Android/iOS

When the above 3 APIs and the relevant SDK are enabled, you can retrieve the API key from the [Credentials page](https://console.cloud.google.com/project/\_/apiui/credential). On the Credentials page, click _Create credentials_ > _API key_.

#### Get Your MapsIndoors API key[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#get-your-mapsindoors-api-key) <a href="#get-your-mapsindoors-api-key" id="get-your-mapsindoors-api-key"></a>

If you are not a customer yet, you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145` to follow this guide, or you can [contact MapsPeople](https://resources.mapspeople.com/contact-us) to get your building drawings processed and hosted by us to receive a unique API key. For the purpose of this guide, both methods will work.

#### Getting Started <a href="#getting-started" id="getting-started"></a>

{% hint style="info" %}
This app is designed to be displayed in Portrait Mode. While it will work in Landscape Mode, some UI elements may look distorted or out-of-place.
{% endhint %}

This app provides an example of how to use the MapsIndoors SDK in SwiftUI.

* Download or clone the pre-made project from GitHub: [https://github.com/MapsPeople/MapsIndoorsTemplate-iOS](https://github.com/MapsPeople/MapsIndoorsTemplate-iOS)
* From the terminal, in the path you cloned the repository to, run `pod install`
* Open the file `xxxxx.xcworkspace` in Xcode, or your editor of choice
* Make a copy of the `MapsIndoors-Info-Sample.plist` file and name it `MapsIndoors-Info.plist`
* Add your MapsIndoors API key and Google Maps API key to the `MapsIndoors-Info.plist` file and run the project

#### Using the Functionality in your Own App[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#using-the-functionality-in-your-own-app) <a href="#using-the-functionality-in-your-own-app" id="using-the-functionality-in-your-own-app"></a>

This project is specifically built so you can easily re-use this functionality in your own application, without further issue.

* To reuse the code in your app, drag and drop the `Views` and `Services` folders, and all the `Assets` except for the `AppIcon` into your project
* Create a copy of the `MapsIndoors-Info-Sample.plist` file and name it `MapsIndoors-Info.plist`
* Add your MapsIndoors API key and Google Maps API key to the `MapsIndoors-Info.plist` file
* Look at `ViewController.swift` for an example of how to initialize the MapsIndoors SDK and use the **MapsIndoors Template** implementation in an UIKit app. The `ViewController.swift` reads the API keys retrieved as described above from the file `MapsIndoors-Info.plist`
* Update your podfile so it matches the podfile in the MapsIndoors **MapsIndoors Template**



{% hint style="warning" %}
1. Developing on the new Arm-based Apple Silicon (M1) Macs requires building and running on a physical iOS device or using an iOS simulator running iOS 13.7, e.g. iPhone 11. This is a temporary limitation in Google Maps SDK for iOS, and as such also a limitation in MapsIndoors, due to the dependency to Google Maps.
2. Due to [a bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/7155) it is necessary to include the `post_install` hook in your Podfile described in the [PodFile post\_install](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install) wiki.
{% endhint %}

#### The Final Result[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#the-final-result) <a href="#the-final-result" id="the-final-result"></a>

![A screenshot of the iOS single page app](https://docs.mapsindoors.com/img/getting-started/iOS\_Single\_Page\_App.png)

### Summary[​](https://docs.mapsindoors.com/getting-started/ios/mapsindoors-template#summary) <a href="#summary" id="summary"></a>

Congratulations! You now have a functioning map in your own app, with the ability to both search for Locations and generate directions! If you want more advanced features, check out [further documentation](https://docs.mapsindoors.com/display-rules/), or modify the existing code from this tutorial to suit your needs!

