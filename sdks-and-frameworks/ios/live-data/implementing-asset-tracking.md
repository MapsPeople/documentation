# Implementing Asset Tracking

There are many potential use cases of the Live Data implementation provided in the SDK. In this guide, we will run through how to implement some Asset Tracking.

Scenarios where asset tracking would be useful could for example be a hospital wishing to keep track of resources such as wheelchairs, both monitoring their location, and whether or not they are currently being used. This is also going to be the example used in this tutorial.

> This tutorial also assumes that you have set up the system and integration that you wish to use to monitor your assets. This tutorial only demonstrates how to display this in MapsIndoors, as accounting for all variations of integrations would be unfeasible. All concepts described in this tutorial can be read about in more detail at [Live Data Introduction for iOS](https://docs.mapsindoors.com/live-data-intro/) and [Live Data Tutorial for iOS](https://docs.mapsindoors.com/live-data-in-practice/).

Enabling Live Data through `mapControl` is an easy way to get Live Data running in your app. For this use case, `MPLiveDomainType.position` and `MPLiveDomainType.availability` should be enabled, as you need to know the position of the wheelchair, and whether or not it is in use - there are several other `MPLiveDomainType` variables that exist, as can be found [here](https://app.mapsindoors.com/mapsindoors/reference/ios/4.2.1/documentation/mapsindoors/mplivedomaintype), but they are not needed for this specific use-case. However, they could be needed for yours!

```
Task {
    try await MPMapsIndoors.shared.load(apiKey: "YOUR API KEY"Y)

    var googleMap: GMSMapView? = nil

    self.googleMap = GMSMapView(frame: CGRect.zero)

    let mapConfig = MPMapConfig(gmsMapView: googleMap!, googleApiKey: "YOUR API KEY")

    mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
    let locations = await MPMapsIndoors.shared.locationsWith(query: nil, filter: nil)
    if let loc = locations.first {
        mapControl?.goTo(entity: loc)
    }
    mapControl?.enableLiveData(domain: MPLiveDomainType.availability, listener: nil)
    mapControl?.enableLiveData(domain: PLiveDomainType.position, listener: nil)
}
```

`mapControl` has a default way of rendering the Live Data Location called by this method. If you wish to render the Live Data in another way, you can add handlers for this, as described in [this section](https://docs.mapsindoors.com/live-data-intro/#rendering-live-data-locations).

Information about how to handle [listening for Live Data Updates](https://docs.mapsindoors.com/live-data-intro/#listening-for-live-updates) could also be extremely relevant for a use-case such as the one described in this tutorial, since a user might need to receive the latest information possible on where available wheelchairs are located.
