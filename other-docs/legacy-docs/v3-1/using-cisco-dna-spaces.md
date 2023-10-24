# Using Cisco DNA Spaces

### MapsIndoors and CiscoDNA for iOS[​](https://docs.mapsindoors.com/cisco-dna/#mapsindoors-and-ciscodna-for-ios-1) <a href="#mapsindoors-and-ciscodna-for-ios-1" id="mapsindoors-and-ciscodna-for-ios-1"></a>

MapsIndoors is a dynamic mapping platform from MapsPeople that can provide maps of your indoor and outdoor localities and helps you create search and navigation experiences for your local users. CiscoDNA is Cisco’s newest digital and cloud-based IT infrastructure management platform. Among many other things, CiscoDNA can pinpoint the physical and geographic position of devices connected wirelessly to the local IT network.

### How User Positioning Works in MapsIndoors for iOS[​](https://docs.mapsindoors.com/cisco-dna/#how-user-positioning-works-in-mapsindoors-for-ios-1) <a href="#how-user-positioning-works-in-mapsindoors-for-ios-1" id="how-user-positioning-works-in-mapsindoors-for-ios-1"></a>

In order to show a user's position in an indoor map with MapsIndoors, a Position Provider must be implemented. MapsIndoors does not implement a Position Provider itself, but rely on 3rd party positioning software to create this experience. In an outdoor environment this Position Provider can be a wrapper of the Core Location Services in iOS.

A Position Provider in MapsIndoors must adhere to the `MPPositionProvider` protocol. Once you have an instance of an `MPPositionProvider` you can register it by assigning it to `MapsIndoors.positionProvider`. See the overview of the interface dependencies below.

![The data flow of our sites to Google](https://docs.mapsindoors.com/img/map/positioning-diagram.png)

### Wrapping the CiscoDNA Positioning in a Position Provider for iOS[​](https://docs.mapsindoors.com/cisco-dna/#wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios-1) <a href="#wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios-1" id="wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios-1"></a>

Just like in the mock Position Provider example, we need to implement a Position Provider that wraps the MapsIndoors CiscoDNA services to inject the CiscoDNA indoor positioning into MapsIndoors. If you only require this to work for indoor positioning, we would be good with just wrapping the CiscoDNA part. MapsIndoors however has the capability to support wayfinding both outdoors and indoors, so the shown solution implements two Position Providers, one for CoreLocation (`GPSPositionProvider`) and one for CiscoDNA (`CiscoDNAPositionProvider2`). The `CiscoDNAPositionProvider2` subclasses the `GPSPositionProvider` so it can determine whether the CiscoDNA position or the Core Location position should be used in your application. Both Position Providers are written in Objective C, but can of course be used in Swift as well.

The `CiscoDNAPositionProvider2` communicates with some MapsIndoors services to get the Cisco device id, and uses a message subscription service (MQTT) to subscribe for position updates. Each time a Cisco position is received, its age is determined. If the age of the latest Cisco position is above 120 seconds or the application is not connected to the wifi, the CoreLocation position is used instead.

### Integration Guide for iOS[​](https://docs.mapsindoors.com/cisco-dna/#integration-guide-for-ios-1) <a href="#integration-guide-for-ios-1" id="integration-guide-for-ios-1"></a>

1. Make sure you have [integrated MapsIndoors](https://docs.mapsindoors.com/cisco-dna/getting-started/ios) succesfully.
2. Download and unzip [this zip file](https://drive.google.com/file/d/1rbtB872NQ81m93xsxdzxA7gn8MtW1Ksl/view?usp=sharing) containing the CiscoDNA integration source.
3. Create a group in your Xcode project, e.g. called CiscoDNA.
4. Drag and drop the files in the downloaded folder to your new group. Choose "Copy items if needed".
5. If this is the first Objective C code in your project, Xcode will suggest that you create an Objective C Bridging Header file. Select "Yes" or "Create Bridging Header".
6. Drag and drop the rest of the files into the CiscoDNA group. Choose "Copy items if needed".
7. In your Objective C Bridging Header, add `#import "CiscoDNAPositionProvider2.h"`.
8.  In `AppDelegate.swift-didFinishLaunchingWithOptions`, add the following code:

    ```
    let pp = MPCiscoDnaPositionProvider2.init()
    pp.tenantId = "my-cisco-dna-spaces-tenant-id"
    MapsIndoors.positionProvider = pp
    MapsIndoors.positionProvider?.startPositioning(nil)
    ```
9. Replace `my-cisco-dna-spaces-tenant-id` with your own Cisco tenant ID.
10. In your view controller displaying the Google Map using `MPMapControl`, call `mapControl.showUserPosition(true)`.
11. Build and run the application. You should now be able to show a blue dot for the user's position.

If you need a working project example with MapsIndoors and CiscoDNA (excluding API keys), you can [download it here](https://drive.google.com/file/d/1nwsdaX0Hm6yaHm5S8JVgqYYb2S0Q4mnT/view?usp=sharing).

**Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/cisco-dna/#fetch-attributes-from-solution-2)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```
MPSolutionProvider().getSolutionWithCompletion { solution, error in
    let providerConfig: Dictionary<String, Dictionary>? = solution?.positionProviderConfigs
}
```

The keys of the outer `Dictionary` are the names of the positioning provider, for example, `indooratlas3` for IndoorAtlas, or `ciscodna` when using Cisco DNA Spaces.

The inner `Dictionary` consists of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.
