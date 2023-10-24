# Switching Solutions

Some larger organisations may have not just multiple Venues, but also multiple Solutions in the MapsIndoors system. Therefore, it is naturally important to be able to switch between them.

At it's core, this is done simply by switching out the API key and reloading the system. However, there are a few more steps that can be done to ensure smooth transition between Solutions.

## Starting a Solution[​](https://docs.mapsindoors.com/switch-solutions#starting-a-solution) <a href="#starting-a-solution" id="starting-a-solution"></a>

When you load your initial Solution, it's beneficial to initialize MapsIndoors properly, to ensure it's easy to switch Solutions later if needed.

```
func setupMapsIndoors(mapsIndoorsAPIKey: String, googleMapsAPIKey: String) {
    map = GMSMapView(frame: CGRect.zero)
    view = map
    mapControl = MPMapControl(map: map!)

    MapsIndoors.provideAPIKey(mapsIndoorsAPIKey, googleAPIKey: googleMapsAPIKey)
    MapsIndoors.synchronizeContent { error in
        // Orient your map to where you need data to be shown. This can e.g. be done by pointing the camera to a specific location or getting the default venue through MapsIndoors and panning the camera there
        self.map?.camera = .camera(withLatitude: 57.057964, longitude: 9.9504112, zoom: 20)

        // Setup needed services
        MapsIndoors.positionProvider = MyPositionProvider()
        MapsIndoors.positionProvider?.startPositioning(nil)
        self.mapControl?.enableLiveData(MPLiveDomainType.occupancy, handler: self)
    }
}
```

#### Switching Solutions[​](https://docs.mapsindoors.com/switch-solutions/#switching-solutions) <a href="#switching-solutions" id="switching-solutions"></a>

To switch Solutions, you first need to ensure that all your existing instances are closed down safely. Once this is done, you can use `setupMapsIndoors` to restart with your new desired API key.

We recommend creating your own function to call in the future for this purpose, like the example here with `switchSolution()`:

```
func switchSolution() {
    // Close existing services in use as they are solution specific
    MapsIndoors.positionProvider?.stopPositioning(nil)
    MapsIndoors.positionProvider = nil
    MPLiveDataManager.sharedInstance().unsubscribeAll()

    // Setup MapsIndoors anew
    map = nil
    mapControl = nil
    setupMapsIndoors(mapsIndoorsAPIKey: "YOUR_SECONDARY_API_KEY", googleMapsAPIKey: "YOUR_GOOGLE_API_KEY")
}
```
