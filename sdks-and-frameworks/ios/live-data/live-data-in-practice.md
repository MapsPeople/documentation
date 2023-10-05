# Live Data in Practice

In this tutorial you will learn to work with Live Data / Real Time Data in MapsIndoors. It is recommended that you read the [Live Data Introduction](https://docs.mapsindoors.com/live-data-intro/) before continueing. It is also recommended that you read [Getting Started](https://docs.mapsindoors.com/getting-started/ios/set-up-your-environment) to see how to setup a MapView and initialize MapsIndoors. To make this example work, you need a valid MapsPeople API key that allows access to live data. Also, don't forget to enable your accessible live data in the domainType variable in the `toggleLiveData()` function e.g.

1. MPLiveDomainType.availability
2. MPLiveDomainType.occupancy
3. MPLiveDomainType.co2
4. MPLiveDomainType.temperature
5. MPLiveDomainType.humidity
6. MPLiveDomainType.count
7. MPLiveDomainType.position

In this example we will use the occupancy and position live data.

Firstly, create a class `LiveDataController` that inherits from `UIViewController`.

```
class LiveDataController: UIViewController {

} 
```

Add buttons for toggling subscriptions, one button for Live Position Updates and one for Live Occupancy Updates.

```
let positionButton = UIButton.init()
let occupancyButton = UIButton.init()
```

Add a method `setupLiveDataButtons()` setting up buttons that enables/disables the subscriptions.

```
fileprivate func setupLiveDataButtons() {
    positionButton.setTitle("See Live Positions", for: .normal)
    positionButton.setTitle("Tracking Live Positions", for: .selected)
    positionButton.addTarget(self, action: #selector(togglePosition), for: .touchUpInside)
    positionButton.backgroundColor = UIColor.blue

    occupancyButton.setTitle("See Live Occupancy", for: .normal)
    occupancyButton.setTitle("Showing Live Occupancy", for: .selected)
    occupancyButton.addTarget(self, action: #selector(toggleOccupancy), for: .touchUpInside)
    occupancyButton.backgroundColor = UIColor.red
}
```

Add a method `toggleLiveData()` that does the actual toggling of Live Data for a button based on the buttons `isSelected` flag. Swap current selected state for button. If the flag is true and the button is selected, call the `MPMapControl.enableLiveData()` method with the given Domain Type. We will also call a `startFlash()`method that should make the button flash. More on this later. If the flag is false and the button is not selected, call the `MPMapControl.disableLiveData()` method with the given Domain Type. Similarly we will call a `stopFlash()`method that should stop the button flash. In this example, we choose to have a customized rendering of Live Data, so we provide a Handler instance that gets the updated Locations. We will get to that later.

```
fileprivate func toggleLiveData(_ button: UIButton, _ domainType: String) {
    button.isSelected = !button.isSelected
    if button.isSelected {
        mapControl?.enableLiveData(domain: domainType, listener: nil)
        button.startFlash()
    } else {
        mapControl?.disableLiveData(domain: domainType)
        button.stopFlash()
    }
}
```

Define an Objective-C method `togglePosition()` that will receive events from your `positionButton`. In this method create a `position` Topic Criteria and call `togglePosition` with the button and the Topic Criteria.

```
@objc func togglePosition(button:UIButton) {
    toggleLiveData(button, MPLiveDomainType.position)
}
```

Define an Objective-C method `toggleOccupancy()` that will receive events from your `occupancyButton`. In this method create a `occupancy` Topic Criteria and call `togglePosition` with the button and the Topic Criteria.

```
@objc func toggleOccupancy(button:UIButton) {
    toggleLiveData(button, MPLiveDomainType.occupancy)
}
```

Inside `viewDidLoad()`, also request a building and go to this building on the map.

```
 Task {
        let locations = await MPMapsIndoors.shared.locationsWith(query: nil, filter: nil)
        if let loc = locations.first {
            mapControl?.goTo(entity: loc)
        }
    }
```

As an example, you can set up your buttons in your viewDidLoad() method. You can also set if up elsaware if thats your wish.

Inside `viewDidLoad()` method, call `setupLiveDataButtons()` arrange the map view and the buttons in stackviews.

```
setupLiveDataButtons()
let buttonStackView = UIStackView.init(arrangedSubviews: [positionButton, occupancyButton])
buttonStackView.axis = .horizontal
buttonStackView.distribution = .fillEqually
let stackView = UIStackView.init(arrangedSubviews: [map!, buttonStackView])
stackView.axis = .vertical
view = stackView

```

Optionally, when you leave this controller, unsubscribe all Live Update Topics.

```
override func viewDidDisappear(_ animated: Bool) {
    mapControl?.disableLiveData(MPLiveDomainType.position)
    mapControl?.disableLiveData(MPLiveDomainType.occupancy)
}
```

Earlier we called some non-existing methods, `startFlash()` and `stopFlash()` on a `UIButton`. We will create these methods now. Create an extension for `UIButton` and add the methods `startFlash()` and `stopFlash()` that creates and adds an animation layer that manipulates with the opacity of the button over time.

```
extension UIButton {
    func startFlash() {
        let flash = CABasicAnimation(keyPath: "opacity")
        flash.duration = 0.5
        flash.fromValue = 1
        flash.toValue = 0.5
        flash.timingFunction = CAMediaTimingFunction(name: kCAMediaTimingFunctionEaseInEaseOut)
        flash.autoreverses = true
        flash.repeatCount = .greatestFiniteMagnitude
        layer.add(flash, forKey: "flash")
    }
    func stopFlash() {
        layer.removeAnimation(forKey: "flash")
    }
}
```

### Expected Result[​](https://docs.mapsindoors.com/live-data-in-practice#expected-result) <a href="#expected-result" id="expected-result"></a>

![Example with temparature, occupancy & availability](https://docs.mapsindoors.com/img/map/liveData\_iOS.png)
