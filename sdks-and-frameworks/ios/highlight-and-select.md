---
description: >-
  This documentation refers to the introduced concept of select and highlight
  that was released with SDK 4.3.0.
---

# Highlight and Select

### How to change the appearance of different states <a href="#how-to-change-the-appearance-of-different-states" id="how-to-change-the-appearance-of-different-states"></a>

The state Display Rules controls how Locations are displayed on the map in different states. For example, you can change the icon scale of a Location when it is hovered over or highlight a search result. The state Display Rules gives access to the same properties as the regular Display Rules, which can be used to control the appearance of Locations.

The iOS SDK supports Select and Highlight Display Rules these can be received and edited through `MapsIndoors`.

#### **Example**

```swift
// Changing the visibility of a polygon for selection
if let displayRule = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .selection) {
    displayRule.polygonVisible = true
}

​// Changing the visibility of a label for highlights
if let displayRule = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .highlight) {
    displayRule.labelVisible = false
}
```

### Highlight <a href="#highlight" id="highlight"></a>

Highlight is for changing the appearance for a collection of `MPLocation`'s, for example to highlight where the restrooms are located in an office building.

#### **Highlight all Restrooms**

```swift
// Create a filter to only receive locations with the category Toilet
let filter =  MPFilter()
filter.categories = ["Toilet"]

// Query locations with the created filter
let locations = await MPMapsIndoors.shared.locationsWith(query: nil, filter: filter)

// Highlighting all current toilets, with default MPHighlightBehavior.
// The MPHighlightBehavior can be used to customize the camera and map behavior when applying it.
// Like fitting the view to show all highlighted locations, if possible.
mapControl?.setHighlight(locations: locations, behavior: .default)
```

<figure><img src="../../.gitbook/assets/ios-highlighted-locations.jpg" alt="" width="563"><figcaption><p>An example of the highlight solution Display Rule in action</p></figcaption></figure>

#### Clear the highlight

```swift
mapControl?.clearHighlight()
```

### Selection <a href="#selection" id="selection"></a>

The selection Display Rule is for changing the appearance of a single Location, for example when the user clicks on it. To select a Location manually, call the method [MPMapControl.select(location:behavior)](https://app.mapsindoors.com/mapsindoors/reference/ios/4.3.2/documentation/mapsindoors/mpmapcontrol/select\(location:behavior:\))

```swift
mapControl?.select(location: location, behavior: .default)
```

<figure><img src="../../.gitbook/assets/ios-selected-location.jpg" alt="" width="563"><figcaption><p>An example of the selection solution DisplayRule in action</p></figcaption></figure>

#### **Clear current selection**

```swift
mapControl?.select(location: nil, behavior: .default)
```

### Previous selection <a href="#previous-selection" id="previous-selection"></a>

Before the release of 4.3.0 the iOS SDK already had a visual implementation of selection with a Display Rule. That can also be changed like the newly introduced Selection Display Rules Type. If you want to retain the old selection rendering, it can be toggled through the Solution Config on the SDK.

#### **Example**

```swift
MPMapsIndoors.shared.solution?.config.newSelection = false
```

Note that this is based on the Solution, so with any subsequent load of MapsIndoors, it will be necessary to set the value to `false` again, as it defaults to `true`.
