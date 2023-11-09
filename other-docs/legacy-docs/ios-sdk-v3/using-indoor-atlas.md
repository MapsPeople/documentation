# Using Indoor Atlas

### MapsIndoors and IndoorAtlas[​](https://docs.mapsindoors.com/indoor-atlas#mapsindoors-and-indooratlas) <a href="#mapsindoors-and-indooratlas" id="mapsindoors-and-indooratlas"></a>

MapsIndoors is a dynamic mapping platform from MapsPeople that can provide maps of your indoor and outdoor localities and helps you create search and navigation experiences for your local users. IndoorAtlas is a MapsPeople partner that works with indoor location based services. Among other things, IndoorAtlas can precisely provide an indoor position with the help of various technologies utilizing various mobile device sensors.

### How User Positioning Works in MapsIndoors[​](https://docs.mapsindoors.com/indoor-atlas#how-user-positioning-works-in-mapsindoors) <a href="#how-user-positioning-works-in-mapsindoors" id="how-user-positioning-works-in-mapsindoors"></a>

In order to show a user's position in an indoor map with MapsIndoors, a Position Provider must be implemented. MapsIndoors does not implement a Position Provider itself, but rely on 3rd party positioning software to create this experience. In an outdoor environment this Position Provider can be a wrapper of the Core Location Services in iOS.

A Position Provider in MapsIndoors must adhere to the `MPPositionProvider` protocol. Once you have an instance of an `MPPositionProvider` you can register it by assigning it to `MapsIndoors.positionProvider`. See the overview of the interface dependencies below.

<figure><img src="https://docs.mapsindoors.com/img/map/positioning-diagram.png" alt=""><figcaption></figcaption></figure>

### Wrapping IndoorAtlas in a Position Provider[​](https://docs.mapsindoors.com/indoor-atlas#wrapping-indooratlas-in-a-position-provider) <a href="#wrapping-indooratlas-in-a-position-provider" id="wrapping-indooratlas-in-a-position-provider"></a>

As previously explained we need to implement a Position Provider that wraps the Indoor Atlas services to inject the indoor positioning into MapsIndoors. We have created an example of such a Position Provider, `IAPositionProvider`, which we will utilize in the following setup instructions. The `IAPositionProvider` is written in Objective C, but can of course be used in Swift as well.

#### Floor Mapping[​](https://docs.mapsindoors.com/indoor-atlas#floor-mapping-1) <a href="#floor-mapping-1" id="floor-mapping-1"></a>

The Position Provider that you supply to MapsIndoors must know about the floor indexes that exist in MapsIndoors. These floor indexes may not exist in the 3rd party system that provides the indoor position. In order to account for this, we have created a floor mapping in the provider, which is basically a lookup table that can give you the MapsIndoors floor index based on another index or id. The mapping is illustrated below:

```
// { IndoorAtlas floor index : MapsIndoors floor index }
{
  0:0,
  1:10,
  2:20,
  3:30,
  4:40
}
```

As illustrated, the floor mapping is a dictionary, where the IndoorAtlas floor index operates as the key and the MapsIndoors floor index is the value.

### Integration Setup Steps[​](https://docs.mapsindoors.com/indoor-atlas#integration-setup-steps) <a href="#integration-setup-steps" id="integration-setup-steps"></a>

1. Make sure you have [integrated MapsIndoors](https://docs.mapsindoors.com/getting-started/ios) succesfully.
2. Download and unzip [this zip file](https://drive.google.com/file/d/1F50FRWBmtHnO9HxASY-T2rkgjRJt7TNm/view?usp=sharing) containing the IndoorAtlas integration source.
3. Create a group in your Xcode project, e.g. called IndoorAtlas.
4. Drag and drop the files in the downloaded folder to your new group. Choose "Copy items if needed".
5. If this is the first Objective C code in your project, Xcode will suggest that you create an Objective C Bridging Header file. Select "Yes" or "Create Bridging Header".
6. In your Objective C Bridging Header, add `#import "IAPositionProvider.h"`.
7. At the time of writing this guide, IndoorAtlas does not support BitCode, so this must be disabled. In the settings, under your XCode project > "Targets" > "Your App Target" > "Build Settings" > "Build Options", set "Enable BitCode" to "No".
8.  In your Apps `Info.plist` file add the following descriptions (preferably right click `Info.plist` and choose "Open as" > "Source Code"):

    ```
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
9.  In `AppDelegate.swift` - `didFinishLaunchingWithOptions`, add the following code:

    ```
    let pp = IAPositionProvider.init(apiKey: "my-indoor-atlas-key", apiSecret: "my-indoor-atlas-secret", floorMapping: [NSNumber(0):NSNumber(0)])
        MapsIndoors.positionProvider = pp
        MapsIndoors.positionProvider?.startPositioning(nil)
    ```
10. In the added code, replace:
    * `my-indoor-atlas-key` with your own IndoorAtlas application key.
    * `my-indoor-atlas-secret` with your own IndoorAtlas application key.
    * `[NSNumber(0):NSNumber(0)]` with the correct floor mapping.
11. In your view controller displaying the Google Map using `MPMapControl`, call `mapControl.showUserPosition(true)`.
12. Build and run the application. You should now be able to show a blue dot for the user's position.

If you need a working project example with MapsIndoors and IndoorAtlas (excluding API keys), you can [download it here](https://drive.google.com/file/d/1CS13NPYjvLl\_Q4zbuinevannsKyuiuBM/view?usp=sharing).

**Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/indoor-atlas#fetch-attributes-from-solution-1)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```
MPSolutionProvider().getSolutionWithCompletion { solution, error in
    let providerConfig: Dictionary<String, Dictionary>? = solution?.positionProviderConfigs
}
```

The keys of the outer `Dictionary` are the names of the positioning provider, for example, `indooratlas3` for IndoorAtlas, or `ciscodna` when using Cisco DNA Spaces.

The inner `Dictionary` consists of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.
