# Custom Floor Selector

### How to implement a custom floor selector[​](https://docs.mapsindoors.com/Map/Map%20Styling/custom-floor-selector#how-to-implement-a-custom-floor-selector) <a href="#how-to-implement-a-custom-floor-selector" id="how-to-implement-a-custom-floor-selector"></a>

To get started, create a class that conforms to both `MPCustomFloorSelector` and `UIView`. Please bear in mind that the provided example is solely for illustrative purposes. It is essential to appropriately integrate and customize it to align with your specific requirements and design preferences.

```swift
// Custom Floor Selector class that conforms to both UIView and MPCustomFloorSelector
class MyFloorSelector: UIView, MPCustomFloorSelector {
    override init(frame: CGRect) {
        super.init(frame: frame)
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }

    func remove() {
        removeFromSuperview()
    }

    var building: MapsIndoors.MPBuilding? {
        didSet {
            if building?.administrativeId != oldValue?.administrativeId {
                createFloorButtons()
                updateCurrentFloorButton()
            }
        }
    }

    var delegate: MapsIndoors.MPFloorSelectorDelegate?
    var floorIndex: NSNumber? {
        didSet {
            updateCurrentFloorButton()
        }
    }

    func onShow() {
        // Shows the floor selector
        isHidden = false
    }

    func onHide() {
        // Hides the floor selector
        isHidden = true
    }

    func createFloorButtons() {
        // Remove existing buttons
        for button in floorButtons {
            button.removeFromSuperview()
        }
        floorButtons.removeAll()

        guard let floors = building?.floors else { return }

        let buttonHeight: CGFloat = 50
        let buttonWidth: CGFloat = 50 // Increased width
        var yOffset: CGFloat = 0
        let xOffset: CGFloat = 20 // Offset from the left of the screen

        // Sort the floors by their floorIndex
        let sortedFloors = floors.sorted { $0.value.floorIndex?.intValue ?? 0 < $1.value.floorIndex?.intValue ?? 0 }

        for (_, floor) in sortedFloors {
            let floorButton = UIButton(frame: CGRect(x: xOffset, y: yOffset, width: buttonWidth, height: buttonHeight))
            floorButton.backgroundColor = .cyan
            floorButton.setTitle(floor.name, for: .normal) // Set the button title to the floor name
            floorButton.addTarget(self, action: #selector(floorButtonTapped), for: .touchUpInside)
            addSubview(floorButton)
            floorButtons.append(floorButton)
            yOffset += buttonHeight
        }
    }

    @objc func floorButtonTapped(sender: UIButton!) {
        guard let floorName = sender.title(for: .normal),
              let floor = building?.floors?.first(where: { $0.value.name == floorName }), // Find the floor using the floor name
              let floorIndex = floor.value.floorIndex else { return }

        // Update the floor index
        self.floorIndex = floorIndex

        // Notify the delegate
        delegate?.onFloorIndexChanged(floorIndex)
    }

    func updateCurrentFloorButton() {
        // Reset the color of the previous current floor button
        currentFloorButton?.backgroundColor = .cyan

        // Find the button corresponding to the current floor using the floor name
        currentFloorButton = floorButtons.first(where: { $0.title(for: .normal) == building?.floors?.first(where: { $0.value.floorIndex == floorIndex })?.value.name })

        // Change the color of the current floor button
        currentFloorButton?.backgroundColor = .blue
    }
}
```

Next step is to initialize and add the class to `MPMapControl`.

```swift
// Initialize a custom floor selector with a CGRect
customFloorSelector = MyFloorSelector(frame: CGRect(x: 40, y: 150, width: floorSelectorWidth, height: floorSelectorHeight))

// Set the mapControl´s floorSelector to your newly created floorSelector
mapControl?.floorSelector = customFloorSelector
```

{% hint style="info" %}
When configuring the floor selector class `MyFloorSelector`, it is necessary to align the x and y coordinates according to your specific requirements. Utilizing constraints to ensure compatibility across various devices is highly recommended.
{% endhint %}

Next step is to handle the floor index. In this example, the floor selector requires knowledge of the current floor index, which can be obtained by reading the value from the current building.

```swift
floorIndex = mapControl?.currentBuilding?.currentFloor
```

{% hint style="warning" %}
It is important that the class representing the custom floor selector you have developed must conform to both the `UIView` and `MPCustomFloorSelector` protocols.
{% endhint %}

### Expected Result <a href="#expected-result" id="expected-result"></a>

<figure><img src="../../.gitbook/assets/ios-custom-floor-selector (1).gif" alt="" width="221"><figcaption></figcaption></figure>
