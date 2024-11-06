# Using Indoor Atlas

### MapsIndoors and IndoorAtlas[​](https://docs.mapsindoors.com/indoor-atlas#mapsindoors-and-indooratlas) <a href="#mapsindoors-and-indooratlas" id="mapsindoors-and-indooratlas"></a>

MapsIndoors is a dynamic mapping platform from MapsPeople that can provide maps of your indoor and outdoor localities and helps you create search and navigation experiences for your local users. IndoorAtlas is a MapsPeople partner that works with indoor location based services. Among other things, IndoorAtlas can precisely provide an indoor position with the help of various technologies utilizing various mobile device sensors.

### How User Positioning Works in MapsIndoors[​](https://docs.mapsindoors.com/indoor-atlas#how-user-positioning-works-in-mapsindoors) <a href="#how-user-positioning-works-in-mapsindoors" id="how-user-positioning-works-in-mapsindoors"></a>

In order to show a user's position in an indoor map with MapsIndoors, a Position Provider must be implemented. MapsIndoors does not implement a Position Provider itself, but rely on 3rd party positioning software to create this experience. In an outdoor environment this Position Provider can be a wrapper of the Core Location Services in iOS.

A Position Provider in MapsIndoors must adhere to the `MPPositionProvider` protocol. Once you have an instance of a `MPPositionProvider` you can register it by assigning it to `MPMapsIndoors.shared.positionProvider`.

### Wrapping IndoorAtlas in a Position Provider[​](https://docs.mapsindoors.com/indoor-atlas#wrapping-indooratlas-in-a-position-provider) <a href="#wrapping-indooratlas-in-a-position-provider" id="wrapping-indooratlas-in-a-position-provider"></a>

As previously explained we need to implement a Position Provider that wraps the Indoor Atlas services to inject the indoor positioning into MapsIndoors. We have created an example of such a Position Provider, `IAPositionProvider`, which we will utilize in the following setup instructions.

#### **Floor Mapping**[**​**](https://docs.mapsindoors.com/indoor-atlas#floor-mapping-1)

The Position Provider that you supply to MapsIndoors must know about the floor indexes that exist in MapsIndoors. These floor indexes may not exist in the 3rd party system that provides the indoor position. In order to account for this, we have created a floor mapping in the provider, which is basically a lookup table that can give you the MapsIndoors floor index based on another index or id. The mapping is illustrated below:

```swift
// [ IndoorAtlas floor index : MapsIndoors floor index ]
[
  0 : 0,
  1 : 10,
  2 : 20,
  3 : 30,
  4 : 40
]
```

As illustrated, the floor mapping is a Dictionary, where the IndoorAtlas floor index operates as the key and the MapsIndoors floor index is the value.

### Integration Setup Steps[​](https://docs.mapsindoors.com/indoor-atlas#integration-setup-steps) <a href="#integration-setup-steps" id="integration-setup-steps"></a>

1. Make sure you have [integrated MapsIndoors](https://docs.mapsindoors.com/getting-started/ios) succesfully.
2. Download and unzip [this zip file](https://drive.google.com/file/d/1r0OPlLf\_YDqWobj80lYtxuuxOtzH6qCq/view?usp=sharing) containing the IndoorAtlas integration source.
3. Create a group in your Xcode project, e.g. called IndoorAtlas.
4. Drag and drop the files in the downloaded folder to your new group. Choose "Copy items if needed".
5.  In your apps `Info.plist` file add the following descriptions (preferably right click `Info.plist` and choose "Open as" > "Source Code"):

    ```xml
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This application uses your location in order to provide wayfinding to indoor facilities.</string>
    <key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
    <string>This application uses your location in order to provide wayfinding to indoor facilities.</string>
    <key>NSLocationAlwaysUsageDescription</key>
    <string>This application uses your location in order to provide wayfinding to indoor facilities.</string>
    <key>NSMotionUsageDescription</key>
    <string>This application uses motion data in order to determine your indoor position.</string>
    <key>NSBluetoothAlwaysUsageDescription</key>
    <string>This application uses BlueTooth in order to determine your indoor position.</string>
    <key>NSBluetoothPeripheralUsageDescription</key>
    <string>This application uses BlueTooth in order to determine your indoor position.</string>
    ```
6.  In `AppDelegate.swift` - `didFinishLaunchingWithOptions`, add the following code:

    ```swift
    let pp = IAPositionProvider(apiKey: "my-indoor-atlas-key",
                                apiSecret: "my-indoor-atlas-secret",
                                floorMapping: [0 : 0])
    MPMapsIndoors.shared.positionProvider = pp
    ```
7. In the added code, replace:
   * `my-indoor-atlas-key` with your own IndoorAtlas application key.
   * `my-indoor-atlas-secret` with your own IndoorAtlas application key.
   * `[0 : 0]` with the correct floor mapping.
8. In your view controller displaying the MapsIndoors Map using `MPMapControl`, call `mapControl.showUserPosition = true`.
9. Build and run the application. You should now be able to show a blue dot for the user's position.

#### **Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/indoor-atlas#fetch-attributes-from-solution-1)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```swift
let providerConfig = MPMapsIndoors.shared.solution?.positionProviderConfigs
```

The keys of the outer `Dictionary` are the names of the positioning provider, for example, `indooratlas3` for IndoorAtlas.

The inner `Dictionary` consists of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.
