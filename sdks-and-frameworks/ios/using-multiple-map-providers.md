# Using multiple Map Providers

It is possible to use `MapsIndoors` with multiple map providers, from this point we will call them platforms, like `Mapbox Maps` and `Google Maps`, in the same app. The basics of this approach is to create a generic interface for accessing the maps, as well as provide some utility in setting up `MapsIndoors`.

In this article we will: Install multiple platforms, create a `Map` interface, create a `Fragment` interface to hold the `MapView`s, create implementations of the `Map` interface for each platform, and then blend them together in a single app.

We will use `Google Maps` and `Mapbox Maps` as examples.

## Add MapsIndoors

First we have to add both platforms to our build file, it is important that both versions of MapsIndoors use the same version, otherwise we might experience issue with the interface.

Add the MapsIndoors Swift Packages for both `mapsindoors-mapbox` and `mapsindoors-googlemaps` to your project using the tools in Xcode (as explained in [Installing the MapsIndoors SDK](https://docs.mapsindoors.com/getting-started/ios/v4/set-up-your-environment#installing-the-mapsindoors-sdk)).

## Map Interface

The map interface should be as lean as possible, only containing the methods you need to use on the maps, in this example we have added 3 methods for moving the camera, as well the ability to read the current position, projection as well as turning the compass on/off.

This interface can be extended or shrunk to your hearts content, but remember that each method added will have to be implemented on both platforms.

```swift
import MapsIndoorsCore

protocol MIMap {
    var bearing: Double { get set }
    var delegate: MIMapDelegate? { get set }
    var mapConfig: MPMapConfig { get }
    var padding: UIEdgeInsets { get set }
    var target: CLLocationCoordinate2D { get set }
    var tilt: Double { get set }
    var view: UIView { get }
    var zoom: Double { get set }
    
    func setup(delegate: MIMapDelegate?)

    func move(target: CLLocationCoordinate2D, zoom: Double, bearing: Double, tilt: Double)
}

protocol MIMapDelegate {
    func cameraMoved()

    func cameraIdle()
}

```

## Map Provider Specific Implementations

The `MIMap` protocol must be implemented for each map provider for seamless switching between them.

For easy access to the API keys and tokens for the map providers and MapsIndoors, we create a `APIKeys` struct.

```swift
struct APIKeys {
    static let googleMaps = "YOUR_GOOGLE_MAPS_API_KEY"
    static let mapboxMaps = "YOUR_MAPBOX_MAPS_ACCESS_TOKEN"
    static let mapsindoors = "YOUR_MAPSINDOORS_API_KEY"
}
```

{% tabs %}
{% tab title="Mapbox Maps" %}

```swift
import MapboxMaps
import MapsIndoorsCore
import MapsIndoorsMapbox

class MapboxMapsWrapper: MIMap {
    var bearing: Double {
        get { map.cameraState.bearing }
        set { map.setCamera(to: CameraOptions(bearing: newValue)) }
    }

    var delegate: MIMapDelegate?

    var mapConfig: MPMapConfig {
        MPMapConfig(mapBoxView: self.mapView, accessToken: APIKeys.mapboxMaps)
    }
    
    var padding: UIEdgeInsets {
        get { map.cameraState.padding }
        set { map.setCamera(to: CameraOptions(padding: newValue)) }
    }

    var target: CLLocationCoordinate2D {
        get { map.cameraState.center }
        set { map.setCamera(to: CameraOptions(center: newValue)) }
    }

    var tilt: Double {
        get { map.cameraState.pitch }
        set { map.setCamera(to: CameraOptions(pitch: newValue)) }
    }

    var view: UIView {
        mapView
    }

    var zoom: Double {
        get { map.cameraState.zoom }
        set { map.setCamera(to: CameraOptions(zoom: newValue)) }
    }
    
    private let map: MapboxMap
    private let mapView: MapView

    required init(frame: CGRect) {
        let options = MapOptions()
        let mapInitOptions = MapInitOptions(mapOptions: options)

        self.mapView = MapView(frame: frame, mapInitOptions: mapInitOptions)
        self.mapView.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        self.mapView.ornaments.options.compass.visibility = .hidden
        self.map = self.mapView.mapboxMap
    }

    func setup(delegate: MIMapDelegate?) {
        self.delegate = delegate
    }

    func move(target: CLLocationCoordinate2D, zoom: Double, bearing: Double, tilt: Double) {
        mapView.camera.fly(to: CameraOptions(center: target, zoom: zoom, bearing: bearing, pitch: tilt))
    }
}
```

{% endtab %}

{% tab title="Google Maps" %}

```swift
import GoogleMaps
import MapsIndoorsCore
import MapsIndoorsGoogleMaps

class GoogleMapsWrapper: NSObject, MIMap {
    var bearing: Double {
        get { map.camera.bearing }
        set {
            guard okToAnimate else { return }
            map.animate(toBearing: CLLocationDirection(floatLiteral: newValue))
        }
    }

    var delegate: MIMapDelegate?

    var mapConfig: MPMapConfig {
        MPMapConfig(gmsMapView: map, googleApiKey: APIKeys.googleMaps)
    }

    var padding: UIEdgeInsets {
        get { map.padding }
        set { map.padding = newValue }
    }

    var target: CLLocationCoordinate2D {
        get { map.camera.target }
        set {
            guard okToAnimate else { return }
            map.animate(toLocation: newValue)
        }
    }

    var tilt: Double {
        get { map.camera.viewingAngle }
        set {
            guard okToAnimate else { return }
            map.animate(toViewingAngle: newValue)
        }
    }

    var view: UIView {
        map
    }

    var zoom: Double {
        get { Double(map.camera.zoom) }
        set {
            guard okToAnimate else { return }
            map.animate(toZoom: Float(newValue))
        }
    }

    private let map: GMSMapView
    private var okToAnimate = false

    required init(frame: CGRect) {
        let options = GMSMapViewOptions()
        options.frame = frame
        self.map = GMSMapView(options: options)
    }

    func setup(delegate: MIMapDelegate?) {
        self.delegate = delegate
        self.map.delegate = self
    }

    func move(target: CLLocationCoordinate2D, zoom: Double, bearing: Double, tilt: Double) {
        guard okToAnimate else { return }

        let position = GMSCameraPosition(target: target, zoom: Float(zoom), bearing: CLLocationDirection(floatLiteral: bearing), viewingAngle: tilt)
        map.animate(with: GMSCameraUpdate.setCamera(position))
    }
}

extension GoogleMapsWrapper: GMSMapViewDelegate {
    @objc public func mapView(_ mapView: GMSMapView, didChange position: GMSCameraPosition) {
        delegate?.cameraMoved()
    }

    @objc public func mapView(_ mapView: GMSMapView, idleAt position: GMSCameraPosition) {
        okToAnimate = true
        delegate?.cameraIdle()
    }

    @objc public func mapView(_ mapView: GMSMapView, willMove gesture: Bool) {
        okToAnimate = false
    }
}
```
{% endtab %}

{% endtabs %}

## ViewController

The created wrappers for the map providers can now be used to show the MapsIndoors data with either Mapbox Maps or Google Maps.

We can create a view controller that sets up the chosen map provider, loads the MapsIndoors data, creates a map control and shows the map.

In this code the map provider is hard coded but you can use a user setting or something else to determine which map provider to use.

```swift
import GoogleMaps
import MapboxMaps
import MapsIndoorsCore
import MapsIndoorsGoogleMaps
import MapsIndoorsMapbox
import UIKit

enum MapProvider {
    case googleMaps
    case mapboxMaps
}

class ViewController: UIViewController {
    private let mapProvider = MapProvider.mapboxMaps
    private var mapViewController: MIMap!
    private var mapControl: MPMapControl?

    override func viewDidLoad() {
        super.viewDidLoad()

        // Set up UI for the requested map provider
        switch mapProvider {
        case .googleMaps:
            setupGoogleMapsProvider()
        case .mapboxMaps:
            setupMapboxMapsProvider()
        }
        view.addSubview(mapViewController.view)
        mapViewController.delegate = self

        // Load MapsIndoors data
        Task {
            try await MPMapsIndoors.shared.load(apiKey: APIKeys.mapsindoors)
            
            // Create MapsIndoors map control
            mapControl = MPMapsIndoors.createMapControl(mapConfig: mapViewController.mapConfig)
            
            // Fetch a random Location to move the map to
            if let firstLocation = await MPMapsIndoors.shared.locationsWith(query: nil, filter: nil).first
            {
                mapControl?.goTo(entity: firstLocation)
            }
        }
    }

    private func setupMapboxMapsProvider() {
        mapViewController = MapboxMapsWrapper(frame: view.bounds)
    }

    private func setupGoogleMapsProvider() {
        GMSServices.provideAPIKey(APIKeys.googleMaps)
        mapViewController = GoogleMapsWrapper(frame: view.bounds)
    }
}

extension ViewController: MIMapDelegate {
    func cameraMoved() {
        // Perform any actions needed when the camera moves
    }

    func cameraIdle() {
        // Perform any actions needed when the camera stops moving
    }
}
```