---
description: iOS V4
---

# Directions Renderer

When getting the result Route from a [Directions Service](https://docs.mapsindoors.com/directions-service/), we may want to display this Route on a map. To perform this task the `MPDirectionsRenderer` can be used.

To configure a `mapConfig` see [Getting Started](https://docs.mapsindoors.com/getting-started/ios/v4/display-a-map/)

This example shows how to setup a query for a route and display the result on a Google Map using the `MPDirectionsRenderer`:

```swift
Task {
    try await MPMapsIndoors.shared.load(apiKey: #INSERT_API_KEY)
            
    mapControl = MPMapsIndoors.createMapControl(mapConfig: #INSERT_YOUR_MAPCONFIG)
            
    let origin = MPMapsIndoors.shared.locationWith(locationId: "a6be6af53db44c1e8cc7fe4f")

    let destination = MPMapsIndoors.shared.locationWith(locationId: "0c44207987174561a53fb00a")
            
    let directionsQuery = MPDirectionsQuery(origin: origin!, destination: destination!)

    let directionsService = MPMapsIndoors.shared.directionsService

    let route = try? await directionsService.routingWith(query: directionsQuery)

    let renderer = mapControl?.newDirectionsRenderer()
    renderer?.fitBounds = true
    renderer?.route = route
    renderer?.animate(duration: 5)
}
```

### Controlling the Visible Segments on the Directions Renderer[​](https://docs.mapsindoors.com/directions-renderer#controlling-the-visible-segments-on-the-directions-renderer-2) <a href="#controlling-the-visible-segments-on-the-directions-renderer-2" id="controlling-the-visible-segments-on-the-directions-renderer-2"></a>

As previously mentioned, the route object is seperated into objects of `MPRouteLeg`. Each leg is again separated into objects of `MPRouteStep`. 

Unless the Route only contains one Leg, the Directions Renderer does not allow the full Route to be rendered all at once. Therefore, if a Leg contains multiple Steps, they will all be shown on the map at the same time, but once the Leg is changed, the previous Steps are not visible anymore.

A specific segment of the route can be rendered by setting the `legIndex` on the `MPDirectionsRenderer`.

```swift
let renderer = mapControl?.newDirectionsRenderer()
renderer?.routeLegIndex = 5
```

The length of the `legs` array determines the possible values of `routeLegIndex` (`0 ..< length`).

#### Reacting to Label Tapping[​](https://docs.mapsindoors.com/directions-renderer#reacting-to-label-tapping-2) <a href="#reacting-to-label-tapping-2" id="reacting-to-label-tapping-2"></a>

The Directions Labels refer to labels shown at the start and/or end of the rendered route segment (leg or step) path, that may provide contextual information or show instructions for the needed user action at that point. E.g. the end label can be retrieved with `.nextRouteLegButton`. The labels are created as simple `UIButton` instances that are rendered as markers on the map. As with most buttons, it is possible to add targets to these labels, so you can react to touch events.

```swift

override func viewDidAppear(_ animated: Bool) {
    let renderer = MPDirectionsRenderer.init()
    renderer.delegate = self
    renderer.map = self.map

    renderer.nextRouteLegButton?.addTarget(self, action: #selector(nextLeg), for: .touchUpInside)
    renderer.previousRouteLegButton?.addTarget(self, action: #selector(previousLeg), for: .touchUpInside)
}

@objc func nextLeg() {
    renderer.routeLegIndex += 1
}

@objc func previousLeg() {
    renderer.routeLegIndex -= 1
}

```

In the above example, a target is added to `nextRouteLegButton` and `nextRouteLegButton` calling the method `nextLeg` and `previousLeg` respectively. These methods then changes the visible Route Leg.

### Show Content of Nearby Locations[​](https://docs.mapsindoors.com/directions-renderer#show-content-of-nearby-locations-2) <a href="#show-content-of-nearby-locations-2" id="show-content-of-nearby-locations-2"></a>

It is possible to show contextual information on the start or end points of the rendered path of a route segment by configuring the directions renderer to look for nearby Locations or POIs.

This is done by creating an appropriate `MPDirectionsRendererContextualInfoSettings` object and passing it to the directions renderer. If the `contextualInfoSettings` property is not set, no contextual information will be searched for and shown.

```swift
class MPDirectionsRendererContextualInfoSettings {
    // The Types of Location that should be used when showing text and icon for a start or end marker.
    // If no Types are supplied, all Types of Locations will be considered.
    var types: [String]?

    // The Categories of Location that should be used when showing text and icon for a start or end marker.
    // If no Categories are supplied, all Categories of Locations will be considered.
    var categories: [String]?

    // The maximum distance in meters allowed for using text and icon from a Location. Leave blank for a default of 5 meters.
    var maxDistance: Double

    // Which content should be used. Default is IconAndName.
    var contentScope: MPDirectionsRendererContextualInfoScope
}
```

Possible values for `contentScope` are `IconAndName` (default), `IconOnly`, or `NameOnly` as defined in `MPDirectionsRendererContextualInfoScope`.

This is an example of how to show information about Locations of Type "Entry" within 20 meters from the route, with both an icon and the name:

```swift
let contextualSettings = DirectionsRendererContextualInfoSettings()
contextualSettings.types = ["Entry"]
contextualSettings.maxDistance = 20
myDirectionsRenderer.contextualInfoSettings = contextualSettings
```
