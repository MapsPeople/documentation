---
description: iOS V4
---

# Directions Service

The class `MPDirectionsService` is used to request routes from one point to another. The minimal required input is an `origin` and a `destination`. You need to build a query using the `MPDirectionsQuery` class.

This example shows how to setup and execute a query for a Route:

```
Task {
  try await MPMapsIndoors.shared.load(apiKey: #INSERT_API_KEY)
          
  mapControl = MPMapsIndoors.createMapControl(mapConfig: #INSERT_YOUR_MAPCONFIG)
          
  let origin = MPPoint(latitude:57.059884140172585, longitude: 9.939936105948238, z: 0)

  let destination = MPPoint(latitude: 57.05718292988392, longitude: 9.930720035736968, z: 0)

  let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)

  let directionsService = MPMapsIndoors.shared.directionsService

  let route = try? await directionsService.routingWith(query: directionsQuery)

  let renderer = mapControl?.newDirectionsRenderer()
  renderer?.fitBounds = true
  renderer?.route = route
  renderer?.animate(duration: 5)
}
```

### Custom properties via renderer[​](https://docs.mapsindoors.com/directions-service#custom-properties-via-renderer) <a href="#custom-properties-via-renderer" id="custom-properties-via-renderer"></a>

The route can be customized via the `directionsRenderer`. A example could be the color of the rendered path and the background color of the renderet line. This can be sat like this:

```
let renderer = mapControl?.newDirectionsRenderer()
renderer?.pathColor = .green
renderer?.backgroundColor = .black
```

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode-2) <a href="#change-transportation-mode-2" id="change-transportation-mode-2"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are five travel modes, **walking**, **bicycling**, **driving**, **transit** (public transportation) and **unknown**. The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set **travel mode** on your request using the `travelMode` property on `MPDirectionsQuery`:

```
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
directionsQuery.travelMode = .driving
```

The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions-2) <a href="#route-restrictions-2" id="route-restrictions-2"></a>

#### Avoiding Stairs and Steps[​](https://docs.mapsindoors.com/directions-service#avoiding-stairs-and-steps-2) <a href="#avoiding-stairs-and-steps-2" id="avoiding-stairs-and-steps-2"></a>

For a wheelchair user or a user with physical disabilities it may be relevant to request a Route that avoids stairs and steps. Avoid certain **way types** on the route using the `avoidWayTypes` property.

```
let directionsQuery = MPDirectionsQuery(originPoint: origin, destinationPoint: destination)
directionsQuery.avoidWayTypes = [.stairs]
```

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions-2) <a href="#app-user-role-restrictions-2" id="app-user-role-restrictions-2"></a>

In the MapsIndoors CMS it is possible to restrict certain ways in the Route Network to be used by users of certain Roles.

It is possible to get the available Roles with help of the `MPSolutionProvider`:

```
MPSolutionProvider.init().getUserRoles { (userRoles, error) in
    let myUserRole = myUserRole.first
}
```

User Roles can be set on a global level using `MapsIndoors.shared.userRoles`.

```
MPMapsIndoors.shared.userRoles = [myUserRole]
```

Setting the user roles globally will effect all services that uses userroles. The `MPDirectionsQuery` will also use the user roles.

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time-2) <a href="#transit-departure-and-arrival-time-2" id="transit-departure-and-arrival-time-2"></a>

Set a **departure date** or an **arrival date** on the route using the `departure` or `arrival` property. This is relevant when using the Transit travel mode. It will only make sense to set one of these properties at a time.

```
let directionsQuery = MPDirectionsQuery(origin: origin, destination: destination)
directionsQuery.departure = Date.init()
```
