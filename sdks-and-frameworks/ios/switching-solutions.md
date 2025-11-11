# Switching Solutions

Some larger organisations may have not just multiple Venues, but also multiple Solutions in the MapsIndoors system. Therefore, it is naturally important to be able to switch between them.

At it's core, this is done simply by switching out the API key and reloading the system. However, there are a few more steps that can be done to ensure smooth transition between Solutions.

## Starting a Solution[​](https://docs.mapsindoors.com/switch-solutions#starting-a-solution) <a href="#starting-a-solution" id="starting-a-solution"></a>

When you load your initial Solution, it's beneficial to initialize MapsIndoors properly, to ensure it's easy to switch Solutions later if needed.

{% tabs %}
{% tab title="Mapbox" %}
```swift
func setupMapsIndoors(mapsIndoorsAPIKey: String, mapboxAccessToken: String) async throws {
    // Set up the Mapbox map view
    let mapInitOptions = MapInitOptions(resourceOptions: ResourceOptions(accessToken: mapboxAccessToken))
    self.mapView = MapView(frame: self.view.bounds, mapInitOptions: mapInitOptions)
    self.view = self.mapView

    // Orient your map to where you need data to be shown.
    // This can e.g. be done by pointing the camera to a specific location (shown below)
    // or getting the default venue after MapsIndoors is loaded and panning the camera there.
    let centerCoordinate = CLLocationCoordinate2D(latitude: 57.057964, longitude: 9.9504112, zoom: 20)
    self.mapView.mapboxMap.setCamera(to: CameraOptions(center: centerCoordinate, zoom: 20))

    // Initialize the MPMapConfig with the Mapbox MapView
    self.mapConfig = MPMapConfig(mapBoxView: mapView, accessToken: mapboxAccessToken)

    try await MPMapsIndoors.shared.load(apiKey: mapsIndoorsAPIKey)

    self.mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
}
```
{% endtab %}

{% tab title="Google Maps" %}
```swift
func setupMapsIndoors(mapsIndoorsAPIKey: String, googleMapsAPIKey: String) async throws {
    // Orient your map to where you need data to be shown.
    // This can e.g. be done by pointing the camera to a specific location (shown below)
    // or getting the default venue after MapsIndoors is loaded and panning the camera there.
    let camera = .camera(withLatitude: 57.057964, longitude: 9.9504112, zoom: 20)
    self.mapView = GMSMapView.map(withFrame: self.view.bounds, camera: camera)
    self.view = self.mapView

    // Initialize the MPMapConfig with the GMSMapView
    self.mapConfig = MPMapConfig(gmsMapView: self.mapView, googleApiKey: googleMapsAPIKey)

    try await MPMapsIndoors.shared.load(apiKey: mapsIndoorsAPIKey)

    self.mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
}
```
{% endtab %}
{% endtabs %}

#### Switching Solutions[​](https://docs.mapsindoors.com/switch-solutions/#switching-solutions) <a href="#switching-solutions" id="switching-solutions"></a>

To switch Solutions you just set up MapsIndoors with a new MapsIndoors API key by calling `setupMapsIndoors` with your new desired API key.

We recommend creating your own function to call in the future for this purpose, like the example here with `switchSolution()`:

{% tabs %}
{% tab title="Mapbox" %}
```swift
func switchSolution() {
    // Setup MapsIndoors anew
    setupMapsIndoors(mapsIndoorsAPIKey: "YOUR_SECONDARY_API_KEY", mapboxAccessToken: "YOUR_MAPBOX_ACCESS_TOKEN")
}
```
{% endtab %}

{% tab title="Google Maps" %}
```swift
func switchSolution() {
    // Setup MapsIndoors anew
    setupMapsIndoors(mapsIndoorsAPIKey: "YOUR_SECONDARY_API_KEY", googleMapsAPIKey: "YOUR_GOOGLE_API_KEY")
}
```
{% endtab %}
{% endtabs %}
