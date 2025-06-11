# Using CrowdConnected

To get started with CrowdConnected positioning, you can either create your own implementation by using the `MPPositionProvider` interface from the MapsIndoors SDK to visualize position updates from CrowdConnected or you can use the MapsIndoors CrowdConnected Positioning Provider, which handles this work for you, requiring minimal setup. This guide shows how to make use of it.

This guide assumes you already have an app with MapsIndoors.

## Add Swift Package to your app project

First, add the MapsIndoors CrowdConnected Swift Package to your dependencies

### Using Swift Package Manager in Xcode

1.  In Xcode, open your project.
2.  Go to `File` -> `Add Package Dependencies…`
3.  Enter the repository URL for the MApsIndoors CrowdConnected package (https://github.com/MapsPeople/mapsindoors-crowdconnected-ios.git)
4.  Follow the prompts to select the package and target for installation.

### Using Swift Package Manager Manifest

Add the following to your `Package.swift` file:

```swift
dependencies: [
    .package(url: "https://github.com/MapsPeople/mapsindoors-crowdconnected-ios.git", .upToNextMajor(from: "1.0.0")),
]
```

### Fork the project

If you prefer more control, want to understand how it works, or want to customize the positioning provider package, you can go to the publicly available GitHub project. The source code is hosted at [MapsIndoors CrowdConnected Package GitHub Repository](https://github.com/MapsPeople/mapsindoors-crowdconnected-ios). By forking the repository, you create your own copy of the project, which you can modify to suit your specific requirements while retaining the base functionality.

## Get iOS permissions

Ensure your `Info.plist` file includes the necessary privacy-sensitive usage descriptions for location services, as required by both the MapsIndoors and CrowdConnected SDKs. This typically includes:

* `NSLocationWhenInUseUsageDescription`
* `NSLocationAlwaysAndWhenInUseUsageDescription` (if your app requires background location)
* `NSBluetoothPeripheralUsageDescription` (since your app needs to use Bluetooth for the CrowdConnected IPS to work)

When you provide the necessary keys and initialize the CrowdConnected Position Provider, the user will be prompted to grant the required permissions.

## Show the user position on the map

The last step to get the positioning shown on the map is to enable it in `MPMapControl`. This is typically done when creating the `MPMapControl`, but it can be turned on at any time. In the following example we turn on positioning when creating our `MPMapControl`.

```swift
import MapsIndoorsCore
import MapsIndoors_CrowdConnected

MPMapsIndoors.shared.positionProvider = CrowdConnectedPositionProvider(appKey: "<YOUR_APP_KEY>", token: "<YOUR_TOKEN>", secret: "<YOUR_SECRET>")
mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
mapControl?.showUserPosition = true
```

If you keep a reference to the position provider, it makes it easy to enable and disable positioning at any time:

```swift
class YourMapController {
  private var positionProvider: CrowdConnectedPositionProvider?
  private var mapControl: MPMapControl?

  init() {
    positionProvider = CrowdConnectedPositionProvider(appKey: "<YOUR_APP_KEY>", token: "<YOUR_TOKEN>", secret: "<YOUR_SECRET>")
    MPMapsIndoors.shared.positionProvider = positionProvider
  }

  func enablePositioning() {
    mapControl?.showUserPosition = true
    positionProvider?.startPositioning()
  }

  func disablePositioning() {
    mapControl?.showUserPosition = false
    positionProvider?.stopPositioning()
  }
}
```

### App Lifecycle

It is important to ensure that your user positioning implementation respects the iOS App Lifecycle. The position provider should be properly started, paused, and resumed according to the app lifecycle to prevent memory leaks or unexpected behavior.

Your lifecycle implementation will be slightly different if your app is using the scene-based life-cycle or app-based life-cycle as described in [Apple's documentation about app life-cycle management](https://developer.apple.com/documentation/uikit/managing-your-app-s-life-cycle). Basically, you will want to consider stopping the position provider when the app is in the background and resuming it when the app is in the foreground by implementing the appropriate lifecycle methods from either `UISceneDelegate` or `UIApplicationDelegate`.

#### Example Using UISceneDelegate

```swift
class MySceneDelegate: UISceneDelegate {

    var crowdConnectedPositionProvider: CrowdConnectedPositionProvider?

    func sceneDidBecomeActive(_ scene: UIScene) {
        crowdConnectedPositionProvider?.startPositioning(nil)
    }

    func sceneDidEnterBackground(_ scene: UIScene) {
        crowdConnectedPositionProvider?.stopPositioning(nil)
    }

}
```
