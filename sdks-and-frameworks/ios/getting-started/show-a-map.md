# Display a Map

Now that we have the prerequisite API keys, and the project set up, we can start adding basic functionality to the app. We will start by having the app display a map.

### Display a Map with MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/map#show-a-map-with-mapsindoors) <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

:::tip Examples

1. Check out Git repo for a full example: [iOS v4 Getting Started](https://github.com/MapsPeople/MapsIndoors-iOS-Examples) and also [iOS v4 Examples](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples)
2. Note: Later in the Examples, `Map Engine` and `Map Provider` can refer to Google Maps and/or Mapbox.

:::

See the full example of Displaying a map here: [DisplayMap.swift](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples/blob/main/MapsIndoorsSDK-iOS-Examples/Getting%20Started/DisplayMap.swift)

In order to accomplish this we will be utilising the [MPMapControl Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpmapcontrol). Open the file, `ViewController.swift` and, once again, add in the following import statements to the top of the file,

#### Import MapsIndoors <a href="#import-mapsindoors" id="import-mapsindoors"></a>



{% tabs %}
{% tab title="Google Maps" %}
```
import MapsIndoorsCore
import MapsIndoorsGoogleMaps
import GoogleMaps
```

Furthermore, provide API Key for Google Maps within AppDelegate and within the ViewController class, make the following changes:



```
   // AppDelegate.swift
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        GMSServices.provideAPIKey(AppDelegate.gApiKey!)
        return true
        }

        // ViewController.swift
        override func viewDidLoad() {
        super.viewDidLoad()
        
        // Set up the Google Maps view. Centered on The White House. Change this to center on a building in your MapsIndoors dataset
        let camera = GMSCameraPosition.camera(withLatitude: 38.8977, longitude: -77.0365, zoom: 20)
        mapView = GMSMapView.map(withFrame: view.bounds, camera: camera)
        view.addSubview(mapView)
        
        // Set up the autoresizing mask to keep the map's frame synced with the view controller's frame.
        mapView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        
        // Initialize the MPMapConfig with the GMSMapView
        mapConfig = MPMapConfig(gmsMapView: mapView, googleApiKey: [YOUR_GOOGLE_API_KEY])
        
        // Initialize the MPMapControl with the MPMapConfig.
        Task {
            // Load MapsIndoors with the MapsIndoors API key.
            try await MPMapsIndoors.shared.load(apiKey: [YOUR_MAPSINDOORS_API_KEY])
            if let mapConfig = mapConfig {
                mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
                // further set up SDK.
                // ...
            }
        }
    }
```

The [MPMapControl Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpmapcontrol) is the connective class between the Map Engine and the MapsIndoors. It allows the two services to collaborate by overlaying the MapsIndoors information over the Map Engine information.

Running the app wihtout the use of code above indeed displays a map, however this is through the use of the Map Engine of your choice exclusively and with a default map region displayed. But we used MapsIndoors to showcase a specific location.

We have now added a _very_ simple search feature! Running the app now should yield a combined map of The White House, showing both the external and internal geographical information. However, let us try and understand what is actually happening.
{% endtab %}

{% tab title="Mapbox" %}


```
import MapsIndoorsCore
import MapsIndoorsMapbox
import Mapbox
```

Furthermore, provide API Key for Google Maps within AppDelegate and within the ViewController class, make the following changes:



```
     override func viewDidLoad() {
        super.viewDidLoad()
        
        // Set up the Mapbox map view
        let mapInitOptions = MapInitOptions(resourceOptions: ResourceOptions(accessToken: AppDelegate.mApiKey))
        mapView = MapView(frame: view.bounds, mapInitOptions: mapInitOptions)
        view.addSubview(mapView)

        // Set up the autoresizing mask to keep the map's frame synced with the view controller's frame.
        mapView.autoresizingMask = [.flexibleWidth, .flexibleHeight]

        // Center the map on a specific location
        let centerCoordinate = CLLocationCoordinate2D(latitude: 38.8977, longitude: -77.0365)
        mapView.mapboxMap.setCamera(to: CameraOptions(center: centerCoordinate, zoom: 20))

        // Initialize the MPMapConfig with the Mapbox MapView
        mapConfig = MPMapConfig(mapBoxView: mapView, accessToken: AppDelegate.mapBoxApiKey!)
        
        // Initialize the MPMapControl with the MPMapConfig.
        Task {
            // Load MapsIndoors with the MapsIndoors API key.
            try await MPMapsIndoors.shared.load(apiKey: AppDelegate.mApiKey)
            if let mapConfig = mapConfig {
                mapControl = MPMapsIndoors.createMapControl(mapConfig: mapConfig)
                // further set up SDK.
                // ...
            }
        }
    }
```

The [MPMapControl Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpmapcontrol) is the connective class between the Map Engine and the MapsIndoors. It allows the two services to collaborate by overlaying the MapsIndoors information over the Map Engine information.

Running the app wihtout the use of code above indeed displays a map, however this is through the use of the Map Engine of your choice exclusively and with a default map region displayed. But we used MapsIndoors to showcase a specific location.

We have now added a _very_ simple search feature! Running the app now should yield a combined map of The White House, showing both the external and internal geographical information. However, let us try and understand what is actually happening.
{% endtab %}
{% endtabs %}

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/v4/display-a-map#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_display-a-map.png)

Feel free to change the query from "White House" to a known building in _your_ MapsIndoors dataset.

Next, let us look into how we can add a search feature and interact with it. We set up a Query and Filter based on the [MPQuery Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpquery) class and [MPFilter Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpfilter) class, respectively. These serve as our search specifications. Finally, we call the MPMapsIndoors.shared.locationsWith(query:query, filter:filter) method, which searches through the full collection of locations based on our previously established query and filter. We then select the desired location and floor index on the map using the mapControl instance. This handles the selection for both the selected Map Engine and MapsIndoors.
