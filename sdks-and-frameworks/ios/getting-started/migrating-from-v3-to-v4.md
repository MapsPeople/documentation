# Migrating from v3 to v4

The iOS SDK for MapsIndoors has received a major update to version 4.0.0, which comes with improved interfaces and flexibility for developing your own map experience. The MapsIndoors SDK now additionally supports Mapbox, alongside some reworked and refactored features that simplify development and SDK behavior. This guide will cover changes to the SDK and how to use it to provide you with a guide on how to upgrade from v3 to v4. _If you have any questions concerning this document, or migrating to v4 in general, please contact MapsPeople technical support._

### MapsIndoors SDK Map Engine Flavors[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#mapsindoors-sdk-map-engine-flavors) <a href="#mapsindoors-sdk-map-engine-flavors" id="mapsindoors-sdk-map-engine-flavors"></a>

With the release of v4, the MapsIndoors SDK is released as multiple separate libraries depending on the Map Provider - Google Maps or Mapbox. You now import the SDK flavor of your choosing with either:

```properties
pod 'MapsIndoorsGoogleMaps', '~> 4.9'
```

or

```properties
pod 'MapsIndoorsMapbox11', '~> 4.9'
```

(Remember to check the [changelog](https://docs.mapsindoors.com/changelogs/ios) for the latest version)

### MapsIndoors Initialization & Usage[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#mapsindoors-initialization--usage) <a href="#mapsindoors-initialization--usage" id="mapsindoors-initialization--usage"></a>

MapsIndoors is a shared instance, which can be described as the data layer of the SDK. Below you will find an example that demonstrates how initialization has been simplified between v3 and v4.

#### Initialization[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#initialization) <a href="#initialization" id="initialization"></a>

**v3**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3)

In v3 you would start the SDK by providing a MapsIndoors API key and a Google API key, in order to start loading a solution.

```swift
MPMapsIndoors.provideAPIKey("YOUR_MAPSINDOORS_API_KEY", googleAPIKey: "YOUR_GOOGLE_API_KEY")
```

In order to wait until MapsIndoors had finished loading the solution, you would call `synchronizeContent` with a completion.

```swift
MPMapsIndoors.synchronizeContent { error in
  // The SDK has finished loading
}
```

In v3 you had no real way to close the MapsIndoors SDK, releasing system resources when no map is shown.

**MapsIndoors Data**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#mapsindoors-data)

In v3, you would access MapsIndoors data via data providers or services, e.g:

```swift
let solution = try await MPSolutionProvider().solution()
                
let venues = try await MPVenueProvider().venues()

let locations1 = try await MPLocationsProvider().locations()
                
let locations2 = try await MPLocationService.sharedInstance().locations(using: MPQuery(), filter: MPFilter())
```

**v4**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4)

In v4, we have improved the interface to initiate the SDK for smaller and safer implementations. In v4 you start loading a solution with a MapsIndoors API by:

```swift
do {
    try await MPMapsIndoors.shared.load(apiKey: "YOUR_MAPSINDOORS_API_KEY")
} catch {
    // Do something with the thrown error
    print(error)
}
```

Note that this is async/await and therefore requires an asynchronous context, and potentially throws an error if there were problems during loading MapsIndoors data. You can therefore be sure on the next line, that the SDK has either successfully loaded, or failed.

Additionally in v4, you can now close the SDK, releasing system resources when MapsIndoors is no longer actively needed for your application’s state.

```swift
MPMapsIndoors.shared.shutdown()
```

The Google API key is no longer set on MapsIndoors - but rather on `MPMapConfig` when creating `MPMapControl`.

**MapsIndoors Data**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#mapsindoors-data-1)

In v4, everything data should be accessed via `MPMapsIndoors.shared` instance

```swift
let solution = MPMapsIndoors.shared.solution

let venues = await MPMapsIndoors.shared.venues()
                
let locations1 = await MPMapsIndoors.shared.locationsWith(query: MPQuery(), filter: MPFilter())
```

### MapControl Initialization[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#mapcontrol-initialization) <a href="#mapcontrol-initialization" id="mapcontrol-initialization"></a>

MapControl instantiation and initialization are separate concepts. You create a new instance of `MPMapControl` and configure it with a map and view.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-1) <a href="#v3-1" id="v3-1"></a>

To create a `MPMapControl` instance in v3, it was really straight forward.

```swift
let mapControl = MPMapControl(map: googleMapView)
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-1) <a href="#v4-1" id="v4-1"></a>

In v4, in order to support multiple map engines, we have changed how you create a `MPMapControl` instance. You now have to call a factory function on `MapsIndoors`, and provide an `MPMapConfig`.

```swift
if let mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig) {
  // MapControl instance
}
```

An `MPMapConfig` is a configurable wrapper around either a Google Maps or Mapbox view. Depending on which flavor of the SDK you are using, either of the following approaches will derive a `MPMapConfig`, which you can use to create a `MPMapControl`:

Google:

```swift
let mapConfig = MPMapConfig(gmsMapView: googleMapsView, googleApiKey: "AIza....")
```

Mapbox:

```swift
let mapConfig = MPMapConfig(mapBoxView: mapboxMapView, accessToken: "mapbox api key")
```

### SolutionConfig & AppConfig[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#solutionconfig--appconfig) <a href="#solutionconfig--appconfig" id="solutionconfig--appconfig"></a>

AppConfig and SolutionConfig can now be accessed with:

```swift
let solutionConfig = MPMapsIndoors.shared.solution?.config
solutionConfig?.collisionHandling = .removeLabelFirst
solutionConfig?.enableClustering = false

let appConfig = await MPMapsIndoors.shared.appData()
```

AppConfig is for applications specific information, you may want to store in MapsIndoors data for use in your application. AppConfig can be edited in the CMS. SolutionConfig is for adjusting SDK behavior and settings, e.g. enable or disable clustering of POIs. SolutionConfig can also be edited in the CMS.

### Display Rules[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#display-rules) <a href="#display-rules" id="display-rules"></a>

Display rules have changed significantly between v3 and v4. The concept remains the same - each location has a rule, describing how it is rendered, and values are inherited from the location’s type’s display rule if not defined in the location’s own display rule.

The following code snippets show how to edit the display rule of a MapsIndoors location, and to undo the change.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-2) <a href="#v3-2" id="v3-2"></a>

**Editing a display rule**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#editing-a-display-rule)

```swift
let location = try await MPLocationsProvider().location(withId: "myLocationId")
if let displayRule = mapControl?.getEffectiveDisplayRule(for: location) {
    displayRule.showPolygon = true
    displayRule.polygonFillColor = .red
}
```

**Resetting display rules**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#resetting-display-rules)

In order to modify a display rule, and later reset it (e.g. if you wanted to highlight the location temporarily), you would have to remember a copy of the display rule prior to modifying it. Then later, you can set the copied display rule onto the location.

```swift
var rememberedRule: MPLocationDisplayRule?

// Modifying for highlight
let location = try await MPLocationsProvider().location(withId: "myLocationId")
if let displayRule = mapControl?.getEffectiveDisplayRule(for: location) {
    rememberedRule = displayRule.copy() as? MPLocationDisplayRule
    displayRule.showPolygon = true
    displayRule.polygonFillColor = .red
}

// Resetting
if let originalDisplayRule = rememberedRule {
    mapControl?.setDisplayRule(originalDisplayRule, for: location)
}
```

**Editing built-in SDK display rules**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#editing-built-in-sdk-display-rules)

You could access the built-in display rules either directly on your `MPMapControl` instance, or by name.

```swift
let selectionHighlight = mapControl?.locationHighlightDisplayRule
let blueDotDisplayRule = mapControl?.getDisplayRule(forTypeNamed:"my-location")
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-2) <a href="#v4-2" id="v4-2"></a>

**Editing a display rule**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#editing-a-display-rule-1)

```swift
if let location = MPMapsIndoors.shared.locationWith(locationId: "myLocationId") {
    if let displayRule = MPMapsIndoors.shared.displayRuleFor(location: location) {
        displayRule.polygonVisible = true
        displayRule.polygonFillColor = .red
    }
}
```

**Resetting display rules**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#resetting-display-rules-1)

```swift
if let location = MPMapsIndoors.shared.locationWith(locationId: "myLocationId") {
    if let displayRule = MPMapsIndoors.shared.displayRuleFor(location: location) {
        displayRule.reset()
    }
}
```

Resetting a display rule will return it to its initial values. Any values you can previously set, will be reverted back to CMS values.

**Editing built-in SDK display rules**[**​**](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#editing-built-in-sdk-display-rules-1)

The SDK has some display rules dedicated for some SDK specific rendering, which cannot be edited in the CMS, e.g. building outline, selection highlight, blue dot. These rules are not bound to any unique location, so instead you can fetch them using `MPDisplayRuleType` enum.

E.g. change the building outline stroke color and width, optionally used to highlight the current building.

```swift
let buildingOutlineRule = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .buildingOutline)
buildingOutlineRule?.polygonStrokeColor = .blue
buildingOutlineRule?.polygonStrokeWidth = 10.0
```

### DirectionsService & DirectionsRenderer[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#directionsservice--directionsrenderer) <a href="#directionsservice--directionsrenderer" id="directionsservice--directionsrenderer"></a>

The jump from v3 to v4 also introduces small differences in route directions querying and rendering.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-3) <a href="#v3-3" id="v3-3"></a>

```swift
var origin = MPPoint(lat: 38.897382, lon: -77.037447, zValue:0)
var destination = MPPoint.init()
private let renderer = MPDirectionsRenderer()

let directions = MPDirectionsService()
let directionsQuery = MPDirectionsQuery(originPoint: origin, destination: destination)

directions.routing(with: directionsQuery) { (route, error) in
    self.renderer.map = self.mapView
    self.renderer.route = route
    self.renderer.routeLegIndex = 0
    self.renderer.animate(5)
}
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-3) <a href="#v4-3" id="v4-3"></a>

```swift
let origin = MPPoint(latitude:57.059884140172585, longitude: 9.939936105948238, z: 0)
let destination = MPPoint(latitude: 57.05718292988392, longitude: 9.930720035736968, z: 0)

let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)

let directionsService = MPMapsIndoors.shared.directionsService
let route = try? await directionsService.routingWith(query: directionsQuery)

let renderer = mapControl?.newDirectionsRenderer()
renderer?.fitBounds = true
renderer?.route = route
renderer?.animate(duration: 5)
```

### App User Roles[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#app-user-roles) <a href="#app-user-roles" id="app-user-roles"></a>

App user roles as a feature remains the same, however the interface for getting available user roles and applying user roles has changed.

In v3 you would use a `MPSolutionProvider` instance to query a list of all available user roles for the loaded MapsIndoors solution. Applying user roles was done by assigning to the `userRoles` property on `MapsIndoors`.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-4) <a href="#v3-4" id="v3-4"></a>

```swift
MPSolutionProvider().getUserRoles { (userRoles, error) in
    if let janitorRole = userRoles.first(where: { role in role.userRoleName == "janitor" }) {
        MapsIndoors.userRoles = [janitorRole]
    }
}
```

In v4 the interface has been moved to the shared `MPMapsIndoors` instance, where the list of available user roles for a given solution can be found. Applying user roles is similar to v3, just assign to the `userRoles` property on the shared instance of `MPMapsIndoors`.

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-4) <a href="#v4-4" id="v4-4"></a>

```swift
let allRoles = MPMapsIndoors.shared.availableUserRoles
            
if let janitorRole = allRoles.first(where: { userRole in userRole.userRoleName == "janitor" }) {
    MPMapsIndoors.shared.userRoles = [janitorRole]
}
```

### Map & Camera Behavior Configs[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#map--camera-behavior-configs) <a href="#map--camera-behavior-configs" id="map--camera-behavior-configs"></a>

In v4, we have introduced `MPFilterBehavior` and `MPSelectionBehavior`. These object contains behavioral configuration to describe how and if the camera should behave. The following can be configured:

* `var moveCamera: Bool { get set }`
* `var showInfoWindow: Bool { get set }`
* `var allowFloorChange: Bool { get set }`
* `var animationDuration: Int { get set }`

E.g. say you want to select a location, but not move the camera to the location:

```swift
let selectionBehavior = MPSelectionBehavior.default
selectionBehavior.moveCamera = false
mapControl.select(location: location, behavior: selectionBehavior)
```

### The "Go-To" Function[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#the-go-to-function) <a href="#the-go-to-function" id="the-go-to-function"></a>

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-5) <a href="#v3-5" id="v3-5"></a>

In v3, there was a convenience method to easily move the camera to a given `MPLocation`.

```swift
if let location = MPLocationsProvider().location(withId: "myLocationId") {
    mapControl?.go(to: location)
}
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-5) <a href="#v4-5" id="v4-5"></a>

In v4 the `goTo(...)` convenience method has been expanded upon, so it can be used with anything adhering to the `MPEntity` protocol, which includes `MPLocation`, `MPBuilding`, `MPVenue`, and even your own data types, as long as they adhere to `MPEntity`.

```swift
if let location = MPMapsIndoors.shared.locationWith(locationId: "myLocationId") {
    mapControl.goTo(entity: location)
}
```

```swift
if let venue = MPMapsIndoors.shared.venueWith(id: "venue id") {
    mapControl.goTo(entity: venue)
}
```

```swift
if let building = MPMapsIndoors.shared.buildingWith(id: "building id") {
    mapControl.goTo(entity: building)
}
```

### Map Filtering[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#map-filtering) <a href="#map-filtering" id="map-filtering"></a>

You can filter content on the map - say you only wanted to show all meeting rooms on the map.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-6) <a href="#v3-6" id="v3-6"></a>

In v3, you could set `searchResults` on your `MPMapControl` instance, to only show a list of locations.

```swift
let query = MPQuery()
let filter = MPFilter()
filter.types = ["MeetingRoom"]

MPLocationService.sharedInstance().getLocationsUsing(query, filter: filter) { (locations, error) in
    mapControl?.searchResult = locations
}
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-6) <a href="#v4-6" id="v4-6"></a>

In v4, you can use an `MPFilter` directly to apply a filter on your `MPMapControl` instance.

```swift
let filter = MPFilter()
filter.types = ["MeetingRoom"]
mapControl.setFilter(filter: filter, behavior: .default)
```

You can also still simply use an array of `MPLocation` to set a filter on the map.

```swift
let filterBehavior = MPFilterBehavior.default
filterBehavior.moveCamera = false
filterBehavior.allowFloorChange = false

mapControl.setFilter(locations: myLocations, behavior: filterBehavior)
```

To clear the map filter, and return to normal displaying, call `clearFilter()`

```swift
mapControl.clearFilter()
```

### Positioning Providers[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#positioning-providers) <a href="#positioning-providers" id="positioning-providers"></a>

The interfaces for using position providers with MapsIndoors, was bloated in v3, so you would have to implement a lot more behavior than was strictly necessary. In v4 we have trimmed down the interface, to ease integrations with positioning systems.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-7) <a href="#v3-7" id="v3-7"></a>

In v3, the `MPPositionProvider` protocol required that you implemented your positioning integration in a particular way.

```swift
protocol MPPositionProvider {
    
    var preferAlwaysLocationPermission: Bool
    
    var locationServicesActive: Bool
    
    var delegate: MPPositionProviderDelegate?
    
    var latestPositionResult: MPPositionResult?
    
    var providerType: MPPositionProviderType
    
    func requestLocationPermissions()
    
    func updateLocationPermissionStatus()
    
    func startPositioning(_ arg: String?)
    
    func stopPositioning(_ arg: String?)
    
    func startPositioning(after millis: Int32, arg: String?)
    
    func isRunning() -> Bool
    
}

protocol MPPositionProviderDelegate {
    
    func onPositionUpdate(_ positionResult: MPPositionResult)
    
    func onPositionFailed(_ provider: Any)
    
}
```

Even the simplest possible provider would look like:

```swift
class MockPositionProvider : NSObject, MPPositionProvider {
    
    var delegate: MPPositionProviderDelegate?
    
    var latestPositionResult: MPPositionResult?
    
    var providerType: MPPositionProviderType = .GPS_POSITION_PROVIDER
    
    var preferAlwaysLocationPermission: Bool = true
    
    var locationServicesActive: Bool = true
    
    private var running = false

    private func updatePosition() {
        if running {
            latestPositionResult = MPPositionResult()
            latestPositionResult?.geometry = MPPoint(lat: Double.random(in: 0...90), lon: Double.random(in: 0...90))
            delegate?.onPositionUpdate(latestPositionResult!)
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                self.updatePosition()
            }
        }
    }
    
    func startPositioning(after millis: Int32, arg: String?) {
        DispatchQueue.main.asyncAfter(deadline: .now() + (0.001 * Double(millis))) {
            self.startPositioning(arg)
        }
    }
    
    func startPositioning(_ arg: String?) {
        running = true
        updatePosition()
    }

    func stopPositioning(_ arg: String?) {
        running = false
    }
    
    func requestLocationPermissions() {
        // request system permissions for positioning
    }
    
    func updateLocationPermissionStatus() {
        // update system permissions status for positioning
    }

    func isRunning() -> Bool {
        return running
    }

}
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-7) <a href="#v4-7" id="v4-7"></a>

For v4, we have simplified the position provider protocol for `MPPositionProvider` and `MPPositionProviderDelegate`, which now looks like:

```swift
public protocol MPPositionProvider {
    var delegate: MPPositionProviderDelegate? { get set }
    var latestPosition: MPPositionResult? { get }
}

public protocol MPPositionProviderDelegate {
    func onPositionUpdate(position: MPPositionResult)
}
```

`MPPositionResult` has also been simplified.

Here is a small example of a mock implementation:

```swift
class MockPositionProvider: MPPositionProvider {
    
    var delegate: MPPositionProviderDelegate?
    
    var latestPosition: MPPositionResult?

    func simulate() {
        latestPosition = MPPositionResult(coordinate: CLLocationCoordinate2D(latitude: Double.random(in: 0...90), longitude: Double.random(in: 0...90)))
        delegate?.onPositionUpdate(position: latestPosition!)
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            self.simulate()
        }
    }
    
}
```

You attach it to the MapsIndoors SDK, by setting it on your `MPMapControl` instance (and enable `showUserPosition`)

```swift
let positionProvider = MockPositionProvider()
mapControl.showUserPosition = true
mapControl.positionProvider = positionProvider

positionProvider.simulate()
```

### Location Data Sources[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#location-data-sources) <a href="#location-data-sources" id="location-data-sources"></a>

Creating custom Location Sources.

#### v3[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v3-8) <a href="#v3-8" id="v3-8"></a>

To create MPLocationUpdate instances, you would simply call the `init`:

```swift
MPLocationUpdate.init(id: ["LOCATION ID"], from: ["LOCATION OBJECT"])
```

Then inside `viewDidLoad` you register your custom sources:

```swift
MapsIndoors.register([
    MyCustomSource.init(type: "Robot")
])
```

#### v4[​](https://docs.mapsindoors.com/getting-started/ios/v4/v4-migration-guide#v4-8) <a href="#v4-8" id="v4-8"></a>

The concept for registering custom location sources is very similar to that of v3. In that you create your own custom Location Sources, however the way to create create `MPLocationUpdate` is slightly different.

```
MPMapsIndoors.createLocationUpdateFactory().updateWithId(id: ["LOCATION ID"], from: ["LOCATION OBJECT"])
```

And for registering the sources, after initializing the SDK, you call the `register` method:

```swift
 Task {
        try await MPMapsIndoors.shared.load(apiKey: ["API KEY"])
        mapControl = MPMapsIndoors.createMapControl(mapConfig: ["MAP CONFIG"])
        /***
        Inside `viewDidLoad`, after initialising the SDK, register the sources
        ***/
        MPMapsIndoors.shared.register([
            MyCustomSource(type: "Robot")
            ])
```
