---
description: Android v4
---

# Directions Service

The class `MPDirectionsService` is used to request routes from one point to another. The minimum required input to receive a route is an `origin` and a `destination`.

This example shows how to setup and execute a query for a Route:

```kotlin
val directionsService = MPDirectionsService()
val directionsRenderer = MPDirectionsRenderer(mapControl)
val origin = MPPoint(57.057917, 9.950361, 0.0)
val destination = MPPoint(57.058038, 9.950509, 0.0)
directionsService.setRouteResultListener { result, error ->
    val route = result?.routes?.firstOrNull() ?: return@setRouteResultListener
    // The result can arrive on a background thread, so render the route from the main thread
    lifecycleScope.launch { directionsRenderer.setRoute(route) }
}
directionsService.query(origin, destination)
```

The route can be customized via the `directionsRenderer` with `MPDirectionsRendererOptions`. An example could be the color of the rendered line and the color of the animation drawn on top of it. This can be set like this:

```kotlin
val directionsRenderer = MPDirectionsRenderer(mapControl)
directionsRenderer.setOptions(
    MPDirectionsRendererOptions(
        strokeColor = Color.GREEN,
        animatedOverlayColor = Color.BLACK,
    )
)
```

### Change Transportation Mode​ <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set the **travel mode** on your request using the `setTravelMode` method on `MPDirectionsService`:

```kotlin
fun createRoute(mpLocation: MPLocation) {
    // If MPDirectionsService has not been instantiated, create it here and listen for its route results.
    val service = directionsService ?: MPDirectionsService().also { service ->
        service.setRouteResultListener { result, error -> onRouteResult(result?.routes?.firstOrNull(), error) }
        directionsService = service
    }
    service.setTravelMode(MPTravelMode.WALKING)
    // Queries the MPDirectionsService for a route from the hardcoded user location to the point of the location.
    service.query(userLocation, mpLocation.point)
}
```

The travel modes generally only apply for outdoor navigation. Indoor navigation calculations are based on the **walking** travel mode.

### Route Restrictions​ <a href="#route-restrictions-2" id="route-restrictions-2"></a>

There are several ways to influence the calculated route by applying various restrictions to the directions query:

* **Avoid** and/or **exclude** certain **way types** under certain circumstances
* Apply restrictions based on User Roles

#### Avoid and exclude way types <a href="#route-restrictions-2" id="route-restrictions-2"></a>

It is possible to avoid and/or exclude certain way types when calculating a route. **Avoiding** a way type means that `DirectionsService` will do its best to find a route without the avoided way types, but if no route can be found without them, it will try to find a route where the avoided way types may be used. To eliminate certain way types entirely, add them as **excluded** way types. If a way type is both avoided and excluded, excluded will be obeyed.

It may for example be desirable to provide a route better suited for users with physical disabilities by avoiding e.g. stairs. This can be achieved by avoiding that **way type** on the route using the `avoidWayTypes` property:

```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.addAvoidWayType(MPHighway.STEPS)
```

In an emergency situation it may be required to not use elevators at all. This can be achieved by adding the way type elevator to the `excludeWayTypes` property:

```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.addExcludeWayType(MPHighway.ELEVATOR)
```

When Route restrictions are set on the `MPDirectionsService` they will be applied to any subsequent queries as well. You can remove them again by calling `clearAvoidWayType` or `clearExcludeWayType`.

```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.clearAvoidWayType()
directionsService.clearExcludeWayType()
```

#### App User Role Restrictions​ <a href="#app-user-role-restrictions" id="app-user-role-restrictions"></a>

In the MapsIndoors CMS it is possible to restrict certain **ways** in the Route Network to only be accessible by users belonging to certain Roles.

You can get the applied Roles with help of the `MapsIndoors.appliedUserRoles` property:

```kotlin
fun getUserRoles(): List<MPUserRole>? = MapsIndoors.appliedUserRoles
```

User Roles can be set on a global level using `MapsIndoors.applyUserRoles`.

```kotlin
fun setUserRoles(userRoles: List<MPUserRole>) {
    MapsIndoors.applyUserRoles(userRoles)
}
```

This will affect all following Directions requests as well as search queries with `MapsIndoors`.

For more information about App User Roles, see [this documentation](https://docs.mapsindoors.com/app-user-roles/).

### Transit Departure and Arrival Time​ <a href="#transit-departure-and-arrival-time" id="transit-departure-and-arrival-time"></a>

When using the Transit travel mode, you must set a **departure date** or an **arrival date** on the route using the `setTime` method on `MPDirectionsService` and declaring if it is a departure or not through `setIsDeparture`. The `date` parameter is the epoch time, in seconds, as an integer, and it is only possible to use one of these properties at a time.

```kotlin
fun setDepartureTime(date: Date?) {
    directionsService.setIsDeparture(true)
    directionsService.setTime(date)
}

fun setArrivalTime(date: Date?) {
    directionsService.setIsDeparture(false)
    directionsService.setTime(date)
}
```
