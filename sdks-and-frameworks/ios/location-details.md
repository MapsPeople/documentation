# Location Details

This is an example of displaying some details of a MapsIndoors location.

Start by creating a `UIViewController` class that conforms to the `MPMapControlDelegate` protocol

```swift
class LocationDetailsController: UIViewController, MPMapControlDelegate {
```

Add a `MPMapControl` to the class

```swift
var mapControl: MPMapControl?
```

Add other views needed for this example

```swift
var detailsView = UIStackView()
var mainView = UIStackView()
var nameLabel = UILabel()
var descrLabel = UILabel()
```

Inside `viewDidLoad`, setup the label views

```swift
mapControl?.delegate = self
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
func didTapInfoWindow(location: MPLocation) -> Bool {
    self.nameLabel.text = location.name
    self.descrLabel.text = location.descr
    return false
}
```

When map is tapped, reset label text

```swift
func didTap(coordinate: MPPoint) -> Bool {
    self.nameLabel.text = nil
    self.descrLabel.text = nil
    return false
}
```
