# Custom Map Padding

### How use map padding[​](https://docs.mapsindoors.com/Map/Map%20Styling/custom-map-padding#how-use-map-padding) <a href="#how-use-map-padding" id="how-use-map-padding"></a>

If your app uses UI components that overlays the map, like a search bar, a bottom sheet or other components, you'll have to add padding to the `MPMapControl`. The first step is to create a new `UIView` or use an existing one.

```swift
// Creating a simple red view for the demo
let testView = UIView()
testView.translatesAutoresizingMaskIntoConstraints = false
testView.backgroundColor = .red
self.view.addSubview(testView)
```

Add Constraints to the newly created `UIView`

```swift
// Adding Auto Layout constraints for the red view
testView.leadingAnchor.constraint(equalTo: self.view.leadingAnchor).isActive = true
testView.trailingAnchor.constraint(equalTo: self.view.trailingAnchor).isActive = true
testView.bottomAnchor.constraint(equalTo: self.view.bottomAnchor).isActive = true
testView.heightAnchor.constraint(equalToConstant: 250).isActive = true
```

Add mapPadding to `mapControl`

```swift
mapControl?.mapPadding = UIEdgeInsets(top: 0, left: 0, bottom: 250, right: 0) 
```

REMEMBER

It's important to notice that you need to adjust the padding so it fits your solution.

### Expected Result[​](https://docs.mapsindoors.com/Map/Map%20Styling/custom-map-padding#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios-map-padding.png)
