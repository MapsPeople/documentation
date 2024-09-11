# Show User's Location aka. Blue Dot

{% hint style="info" %}
For a full implementation of this, please [see here](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples/tree/main/MapsIndoorsSDK-iOS-Examples/Intermediate/Show%20My%20Location).
{% endhint %}

In this tutorial we will show how you can show a blue dot on the map, representing the users location. The position will be served from a mocked positioning provider and displayed on a map in a view controller.

We will start by creating our implementation of a positioning provider.

Create a class `MyPositionProvider` that  implements `MPPositionProvider`.

```swift
class MyPositionProvider: MPPositionProvider {
```

Add some member variables to `MyPositionProvider`.

* `delegate`: The delegate object
* `running`: A running state boolean flag
* `latestPosition`: The latest positioning result

Create a method called `updatePosition`. This will be our "loop" constantly posting a new position to the delegate.

* Check if the provider has a running state
* Assign a new `MPPositionResult` to `latestPosition`
* Assign a new position point
* Optionally specify that heading is available and set a heading
* Notify the delegate by calling `onPositionUpdate` passing the new position as argument
* Schedule a new delayed call of this method

```swift
func updatePosition() {
    if running {
        latestPosition?.bearing = (latestPosition!.bearing + 10)
        latestPosition?.coordinate = generateRandomCoordinate()
        latestPosition?.bearing = Double.random(in: 0..<360)
        latestPosition?.accuracy = Double.random(in: 0..<10)
            
        if let delegate = self.delegate, let latestPosition = self.latestPosition {
                delegate.onPositionUpdate(position: latestPosition)
        }
        weak var _self = self
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                _self?.updatePosition()
        }
    }
}
```

Implement the `startPositioning` method. We set the `running` boolean to true and call `updatePos`.

```swift
func startPositioning() {
    running = true
    updatePosition()
}
```

Implement the `stopPositioning` method. We set the `running` boolean to false.

```swift
func stopPositioning() {
    running = false
}
```

### Create a view controller displaying a map that shows the user's "mocked" location[​](https://docs.mapsindoors.com/blue-dot/#create-a-view-controller-displaying-a-map-that-shows-the-users-mocked-location) <a href="#create-a-view-controller-displaying-a-map-that-shows-the-users-mocked-location" id="create-a-view-controller-displaying-a-map-that-shows-the-users-mocked-location"></a>

Create a class `ShowMyLocationController` that inherits from `UIViewController`.

```swift
class ShowMyLocationController: UIViewController {
```

Inside `viewDidLoad` or any method of your app's lifecycle, generate and apply a random position, you may optionally also override the default icon for blue dot

```swift
 if let validPosition = await getRandomLocation()?.position {
    let provider = MyPositionProvider(mockAt: validPosition)

    MPMapsIndoors.shared.positionProvider = provider
    mapControl?.showUserPosition = true

    provider.startPositioning()

    // Optionally override the icon for the blue dot by supplying another image
    if let dr = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .blueDot)
    {
        dr.icon = UIImage(named: "MyOwnPositionIcon")
    }
}
```

Inside `viewDidLoad` we

* Tell `mapControl` to show the users location
* Assign your position provider `MyPositionProvider` to `MPMapsIndoors.shared.positionProvider` and then finally,
* Start positioning
