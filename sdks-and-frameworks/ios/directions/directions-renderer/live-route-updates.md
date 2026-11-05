---
description: iOS v4
---

# Live Route Updates

When staff close a door, open it again or set a wait time, a route shown on a directions renderer can update by itself. The SDK listens for route network changes in the loaded solution, requests a shown route again when its graph changed, and replaces it if the new route differs.

## Enabling

The feature is off by default. Turn it on once, for example before or after loading MapsIndoors:

```swift
MPMapsIndoors.shared.liveRouteUpdatesEnabled = true
try await MPMapsIndoors.shared.load(apiKey: "YOUR_API_KEY")
```

Then show a route from `MPMapsIndoors.shared.directionsService` on a directions renderer, as usual:

```swift
let query = MPDirectionsQuery(origin: origin, destination: destination)
let route = try await MPMapsIndoors.shared.directionsService.routingWith(query: query)

let renderer = mapControl.newDirectionsRenderer()
renderer.route = route
renderer.render()
```

## Knowing when the route changed

Implement the optional `onRouteChanged(oldRoute:newRoute:)` on `MPDirectionsRendererDelegate`. It is called on the main thread, after the new route is shown:

```swift
final class NavigationController: NSObject, MPDirectionsRendererDelegate {
    func onRouteChanged(oldRoute: MPRoute, newRoute: MPRoute) {
        // Refresh step lists, arrival times and the like from newRoute.
    }
}

renderer.delegate = navigationController
```

When the SDK replaces the route, the user keeps their place:

* If only the distance or duration changed, as after a wait time change, the route is swapped in place. The selected leg, step, floor and camera stay.
* Otherwise, with live positioning (a position from your `MPPositionProvider` in the last minute) the new route starts just ahead of the user and its first leg is selected. Without live positioning, the step closest to the one the user was viewing is selected.
* The camera does not move. The floor changes only if the selected leg's floor changed.

Set `timestamp` on the `MPPositionResult`s your position provider reports, so the SDK knows how old a position is. Without it, the SDK uses the time it received the position.

## Applying the new route yourself

To decide yourself whether and when to show the new route, turn off automatic apply on the renderer's options. The renderer then keeps the old route and only calls the delegate:

```swift
let options = MPDirectionsRendererOptions()
options.liveRouteUpdatesAutoApply = false
renderer.options = options
```

```swift
func onRouteChanged(oldRoute: MPRoute, newRoute: MPRoute) {
    // For example, ask the user first.
    renderer.route = newRoute
    renderer.render()
}
```

A route applied this way is still updated by later changes.

{% hint style="info" %}
Assigning a new `MPDirectionsRendererOptions` replaces every option, so set `liveRouteUpdatesAutoApply` on each options object you assign.
{% endhint %}

## What it does not do

* Only routes produced by `directionsService` are updated. A route your app built or decoded itself is not.
* Only routes shown on a directions renderer from `mapControl.newDirectionsRenderer()` are updated. If you implement `MPDirectionsRenderer` yourself, the SDK does not see your routes.
* It needs a connection. A device that is offline routes on the graph it last synchronized until the next `load(apiKey:)` or `synchronize()`.
* It is not a safety mechanism. A congested network can delay or drop both the change notification and the new route, and an app that turns automatic apply off and lets the user dismiss the new route leaves them on a route through the closed element.
* It does not change how Live Data works for your app. The route element messages are not delivered to Live Data delegates.

## Insights

A replaced route ends the current route usage and starts a new one, as setting a route does, and both events carry `reroute` = `live_update`. An in-place swap sends no events.
