# Location Details

This is an example of displaying some details of a MapsIndoors location

Start by creating a `UIViewController` class that conforms to the `GMSMapViewDelegate` protocol

```swift
class LocationDetailsController: UIViewController, GMSMapViewDelegate {
```

Add a `GMSMapView` and a `MPMapControl` to the class

```swift
var map: GMSMapView? = nil
var mapControl: MPMapControl? = nil
```

Add other views needed for this example

```swift
var detailsView = UIStackView()
var mainView = UIStackView()
var nameLabel = UILabel()
var descrLabel = UILabel()
```

Inside `viewDidLoad`, setup the map and the mapControl instance:

```swift
self.map = GMSMapView(frame: CGRect.zero)
self.map?.delegate = self
self.map?.camera = .camera(withLatitude: 57.057964, longitude: 9.9504112, zoom: 20)
self.mapControl = MPMapControl(map: self.map!)
```

Setup the label views

```swift
nameLabel = UILabel()
descrLabel = UILabel()
nameLabel.backgroundColor = UIColor.white
descrLabel.backgroundColor = UIColor.white
```

Arrange the labels inside a stackview

```swift
detailsView = UIStackView(arrangedSubviews: [nameLabel, descrLabel])
detailsView.axis = .vertical
```

Arrange the map and the stackview inside another stackview

```swift
mainView = UIStackView(arrangedSubviews: [map!, detailsView])
mainView.axis = .vertical
```

When marker is tapped, get related MapsIndoors location object and set label text, based on the name and description of the location

```swift
func mapView(_ mapView: GMSMapView, didTap marker: GMSMarker) -> Bool {
    let location = mapControl?.getLocation(marker)
    if location != nil {
        self.nameLabel.text = location?.name
        self.descrLabel.text = location?.descr
    }
    return false
}
```

When map is tapped, reset label text

```swift
func mapView(_ mapView: GMSMapView, didTapAt coordinate: CLLocationCoordinate2D) {
    self.nameLabel.text = nil
    self.descrLabel.text = nil
}
```

[See the sample in LocationDetailsController.swift](https://github.com/MapsIndoors/MapsIndoorsIOS/blob/master/Example/DemoSamples/Location%20Details/LocationDetailsController.swift)

[\
](https://docs.mapsindoors.com/location-data-sources)
