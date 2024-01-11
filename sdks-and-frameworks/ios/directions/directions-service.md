---
description: iOS V4
---

# Directions Service

The class `MPDirectionsService` is used to request routes from one point to another. The minimal required input is an `origin` and a `destination`. You need to build a query using the `MPDirectionsQuery` class.

This example shows how to setup and execute a query for a route:

```swift
try await MPMapsIndoors.shared.load(apiKey: #INSERT_API_KEY)
          
mapControl = MPMapsIndoors.createMapControl(mapConfig: #INSERT_YOUR_MAPCONFIG)
          
let origin = MPPoint(latitude:57.059884140172585, longitude: 9.939936105948238, z: 0)
let destination = MPPoint(latitude: 57.05718292988392, longitude: 9.930720035736968, z: 0)
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
let route = try? await directionsService.routingWith(query: directionsQuery)

let renderer = mapControl?.newDirectionsRenderer()
renderer?.fitBounds = true
renderer?.route = route
renderer?.animate(duration: 5)
```

The route can be customized via the `directionsRenderer`. An example could be the color of the rendered path and the background color of the rendered line. This can be set like this:

```swift
let renderer = mapControl?.newDirectionsRenderer()
renderer?.pathColor = .green
renderer?.backgroundColor = .black
```

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode-2) <a href="#change-transportation-mode-2" id="change-transportation-mode-2"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are five travel modes, **walking**, **bicycling**, **driving**, **transit** (public transportation) and **unknown**. The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set **travel mode** on your request using the `travelMode` property on `MPDirectionsQuery`:

```swift
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
directionsQuery.travelMode = .driving
```

The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions-2) <a href="#route-restrictions-2" id="route-restrictions-2"></a>

There are several ways to influence the calculated route by applying various restrictions to the directions query:

* **Avoid** and/or **exclude** certain **way types** under certain circumstances
* Apply restrictions based on User Roles

#### Avoid and exclude way types <a href="#route-restrictions-2" id="route-restrictions-2"></a>

It is possible to avoid and/or exclude certain way types when calculating a route. **Avoiding** a way type means that `DirectionsService` will do its best to find a route without the avoided way types, but if no route can be found without them, it will try to find a route where the avoided way types may be used. To eliminate certain way types entirely, add them as **excluded** way types. If a way type is both avoided and excluded, excluded will be obeyed.

It may for example be desirable to provide a route better suited for users with physical disabilities by avoiding e.g. stairs. This can be achieved by avoiding that **way type** on the route using the `avoidWayTypes` property:

```swift
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
directionsQuery.avoidWayTypes = [.stairs]
```

In an emergency situation it may be required to not use elevators at all. This can be achieved by adding the way type elevator to the `excludeWayTypes` property:

```swift
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
directionsQuery.excludeWayTypes = [.elevator]
```

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions-2)

In the MapsIndoors CMS it is possible to restrict certain ways in the route network to be used by users of certain user roles.

It is possible to get the available user roles with help of the `MPSolutionProvider`:

```swift
MPSolutionProvider().getUserRoles { (userRoles, error) in
    let myUserRole = myUserRole.first
}
```

User roles can be set on a global level using `MapsIndoors.shared.userRoles`.

```swift
MPMapsIndoors.shared.userRoles = [myUserRole]
```

Setting the user roles globally will effect all services that uses user roles. The `MPDirectionsService` will also use the user roles.

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time-2) <a href="#transit-departure-and-arrival-time-2" id="transit-departure-and-arrival-time-2"></a>

Set a **departure date** or an **arrival date** on the route using the `departure` or `arrival` property. This is relevant when using the Transit travel mode. It will only make sense to set one of these properties at a time.

```swift
let directionsQuery = MPDirectionsQuery(origin: origin, destination: destination)
directionsQuery.departure = Date()
```
