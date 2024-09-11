# Using Cisco DNA Spaces

### How User Positioning Works in MapsIndoors for iOS[​](https://docs.mapsindoors.com/cisco-dna/#how-user-positioning-works-in-mapsindoors-for-ios) <a href="#how-user-positioning-works-in-mapsindoors-for-ios" id="how-user-positioning-works-in-mapsindoors-for-ios"></a>

In order to show a user's position in an indoor map with MapsIndoors, a Position Provider must be implemented. MapsIndoors does not implement a Position Provider itself, but rely on 3rd party positioning software to create this experience. In an outdoor environment this Position Provider can be a wrapper of the Core Location Services in iOS.

A Position Provider in MapsIndoors must adhere to the `MPPositionProvider` protocol. Once you have an instance of an `MPPositionProvider` you can register it by assigning it to `MapsIndoors.positionProvider`. See the overview of the interface dependencies below.

### MapsIndoors and CiscoDNA for iOS[​](https://docs.mapsindoors.com/cisco-dna/#mapsindoors-and-ciscodna-for-ios) <a href="#mapsindoors-and-ciscodna-for-ios" id="mapsindoors-and-ciscodna-for-ios"></a>

MapsIndoors is a dynamic mapping platform from MapsPeople that can provide maps of your indoor and outdoor localities and helps you create search and navigation experiences for your local users. CiscoDNA is Cisco’s newest digital and cloud-based IT infrastructure management platform. Among many other things, CiscoDNA can pinpoint the physical and geographic position of devices connected wirelessly to the local IT network.

### Wrapping the CiscoDNA Positioning in a Position Provider for iOS[​](https://docs.mapsindoors.com/cisco-dna/#wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios) <a href="#wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios" id="wrapping-the-ciscodna-positioning-in-a-position-provider-for-ios"></a>

Just like in the mock Position Provider example, we need to implement a Position Provider that wraps the MapsIndoors CiscoDNA services to inject the CiscoDNA indoor positioning into MapsIndoors. If you only require this to work for indoor positioning, we would be good with just wrapping the CiscoDNA part. MapsIndoors however has the capability to support wayfinding both outdoors and indoors, so the shown solution implements two Position Providers, one for CoreLocation (`CoreLocationPositionProvider`) and one for CiscoDNA (`PureCiscoDNAProvider`). The `PureCiscoDNAProvider` subclasses the `MPPositionProvider`; you can have it subclass `CoreLocationPositionProvider` as well so it can determine whether the CiscoDNA position or the Core Location position should be used in your application. Both Position Providers are written in Swift.

The `PureCiscoDNAProvider` communicates with some MapsIndoors services to get the Cisco device id, and uses a message subscription service (MQTT) to subscribe for position updates. Each time a Cisco position is received, its age is determined. If the age of the latest Cisco position is above 120 (you may modify this for your use case) seconds or the application is not connected to the wifi, or if Cisco is not present in the CMS, i.e., no Tenant ID is set, then the CoreLocation position is used instead.

### Integration Guide for iOS[​](https://docs.mapsindoors.com/cisco-dna/#integration-guide-for-ios) <a href="#integration-guide-for-ios" id="integration-guide-for-ios"></a>

1. Make sure you have [integrated MapsIndoors](https://docs.mapsindoors.com/cisco-dna/getting-started/ios) succesfully.
2. Download or clone [this Swift file](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples/blob/main/MapsIndoorsSDK-iOS-Examples/Advanced/Position%20Provider/CiscoDNA/PureCiscoDNAProvider.swift) containing the CiscoDNA integration source.
3. Create a group in your Xcode project, e.g. called CiscoDNA.
4. Drag and drop the file in the downloaded folder to your new group. Choose "Copy items if needed".
5.  In your app where you setup your position provider, add the following code:

    ```swift
    let dnaPositionProvider = PureCiscoDNAProvider()
    dnaPositionProvider.tenantId = "my-cisco-dna-spaces-tenant-id"
    MPMapsIndoors.shared.positionProvider = dnaPositionProvider
    dnaPositionProvider.startPositioning(nil)
    ```
6. Replace `my-cisco-dna-spaces-tenant-id` with your own Cisco tenant ID.
7. In your view controller displaying the Map View using `MPMapControl`, call `mapControl.showUserPosition(true)`.
8. Build and run the application. You should now be able to show a blue dot for the user's position.

**Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/cisco-dna/#fetch-attributes-from-solution-1)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```swift
guard let configs = MPMapsIndoors.shared.solution?.positionProviderConfigs,
      let ciscodnaConfig = configs["ciscodna"],
      let tenantId = ciscodnaConfig["ciscoDnaSpaceTenantId"] as? String else {
        return nil
    }
}
```

The keys of the outer `Dictionary` are the names of the positioning provider, for example, `indooratlas3` for IndoorAtlas, or `ciscodna` when using Cisco DNA Spaces.

The inner `Dictionary` consists of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.

[\
](https://docs.mapsindoors.com/blue-dot)
