---
description: >-
  Often you may want to get directions starting from a user's actual current
  position, instead of from another fixed Location. The following code snippet
  gives an example on how to implement this.
---

# User's Location as Point of Origin

First, [initialize a `PositionProvider`](https://docs.mapsindoors.com/blue-dot/), which can be used with `MPDirectionsQuery` to achieve the desired result.

In order to use the `PositionProvider` to extract a `MPPoint`, you need to use `mapControl?.positionProvider?.latestPosition?.coordinate`. This will allow you to make a MPPoint, using the coordinates from the `positionProvider`.

```swift
if let userPosition = mapControl?.positionProvider?.latestPosition?.coordinate {
    let userPositionToMPPoint = MPPoint(latitude: userPosition.latitude, longitude: userPosition.longitude)
    let destination = MPPoint(latitude: 57.05803, longitude: 9.950509, z: 0.00)
    let directionsQuery = MPDirectionsQuery(originPoint: userPositionToMPPoint, destinationPoint: destination)
    let directionsService = MPMapsIndoors.shared.directionsService
    let route = try? await directionsService.routingWith(query: directionsQuery)
    let rendere = mapControl?.newDirectionsRenderer()
    rendere?.route = route
}
```

Further details on how user positioning works, and how to display it, can be found [here](https://docs.mapsindoors.com/blue-dot/).

This results in directions queries originating from the user's current location.
