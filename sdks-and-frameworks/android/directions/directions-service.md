---
description: Android v4
---

# Directions Service

{% tabs %}
{% tab title="Java" %}
The class `MPDirectionsService` is used to request routes from one point to another. The minimum required input to receive a route is an `origin` and a `destination`. This example shows how to setup and execute a query for a Route:

```java
MPDirectionsService directionsService = new MPDirectionsService(mContext);
MPDirectionsRenderer directionsRenderer = new MPDirectionsRenderer(mMapControl);
MPPoint origin = new MPPoint(57.057917, 9.950361, 0.0);
MPPoint destination = new MPPoint(57.058038, 9.950509, 0.0);
directionsService.setRouteResultListener((route, error) -> {
});
directionsService.query(origin, destination);
```

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set the **travel mode** on your request using the `setTravelMode` method on `MPDirectionsService`:

```java
void createRoute(MPLocation mpLocation) {
    //If MPDirectionsService has not been instantiated create it here and assign the results call back to the activity.
    if (mpDirectionsService == null) {
        mpDirectionsService = new MPDirectionsService(mContext);
        mpDirectionsService.setRouteResultListener(this::onRouteResult);
    }
    mpDirectionsService.setTravelMode(MPTravelMode.WALKING);
      //Queries the MPDirectionsService for a route with the hardcoded user location and the point from a location.
    mpDirectionsService.query(mUserLocation, mpLocation.getPoint());
}
```

The travel modes generally only apply for outdoor navigation. Indoor navigation calculations are based on the **walking** travel mode.

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions) <a href="#route-restrictions" id="route-restrictions"></a>

#### Avoiding Stairs and Steps[​](https://docs.mapsindoors.com/directions-service#avoiding-stairs-and-steps) <a href="#avoiding-stairs-and-steps" id="avoiding-stairs-and-steps"></a>

For wheelchair users or other users with limited mobility, it may be relevant to request a Route that avoids stairs and steps. Avoid certain **way types** on the route using the `addRouteRestriction` method on `MPDirectionsService`.

```java
void getRoute() {
  MPDirectionsService directionsService = new MPDirectionsService(mContext);
  MPDirectionsRenderer directionsRenderer = new MPDirectionsRenderer(mMapControl);
  MPPoint origin = new MPPoint(57.057917, 9.950361, 0.0);
  MPPoint destination = new MPPoint(57.058038, 9.950509, 0.0);
  directionsService.addAvoidWayType(MPHighway.STEPS);
  directionsService.addAvoidWayType(MPHighway.ELEVATOR);
  directionsService.setRouteResultListener((route, error) -> {
  });
  directionsService.query(origin, destination);
}
```

When Route restrictions are set on the `MPDirectionsService` they will be applied to any subsequent queries as well. You can remove them again by calling `clearRouteRestrictions`.

```java
void clearRouteRestrictions() {
  mpDirectionsService.clearRouteRestrictions();
}
```

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions) <a href="#app-user-role-restrictions" id="app-user-role-restrictions"></a>

In the MapsIndoors CMS it is possible to restrict certain **ways** in the Route Network to only be accessible by users belonging to certain Roles.

You can get the available Roles with help of the `MapsIndoors.getAppliedUserRoles`:

```java
List<MPUserRole> getUserRoles() {
  return MapsIndoors.getAppliedUserRoles();
}
```

User Roles can be set on a global level using `MapsIndoors.applyUserRoles`.

```java
void setUserRoles(List<MPUserRole> userRoles) {
    MapsIndoors.applyUserRoles(userRoles);
}
```

This will affect all following Directions requests as well as search queries with `MapsIndoors`.

For more information about App User Roles, see [this documentation](https://docs.mapsindoors.com/app-user-roles/).

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time) <a href="#transit-departure-and-arrival-time" id="transit-departure-and-arrival-time"></a>

When using the Transit travel mode, you must set a **departure date** or an **arrival date** on the route using the `setTime` method on `MPDirectionsService` and declaring if it is a departure or not through `setIsDeparture`. The `date` parameter is the epoch time, in seconds, as an integer, and it is only possible to use one of these properties at a time.

```java
void setDepartureTime(Date date) {
    mpDirectionsService.setIsDeparture(true);
    mpDirectionsService.setTime(date);
}
void setArrivalTime(Date date) {
    mpDirectionsService.setIsDeparture(false);
    mpDirectionsService.setTime(date);
}
```
{% endtab %}

{% tab title="Kotlin" %}
The class `MPDirectionsService` is used to request routes from one point to another. The minimum required input to receive a route is an `origin` and a `destination`. This example shows how to setup and execute a query for a Route:

```kotlin
val directionsService = MPDirectionsService(mContext)
val directionsRenderer = MPDirectionsRenderer(mMapControl)
val origin = MPPoint(57.057917, 9.950361, 0.0)
val destination = MPPoint(57.058038, 9.950509, 0.0)
directionsService.setRouteResultListener { route, error -> }
directionsService.query(origin, destination)
```

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode-1) <a href="#change-transportation-mode-1" id="change-transportation-mode-1"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set the **travel mode** on your request using the `setTravelMode` method on `MPDirectionsService`:

```kotlin
fun createRoute(mpLocation: MPLocation) {
    //If MPDirectionsService has not been instantiated create it here and assign the results call back to the activity.
    if (mpDirectionsService == null) {
        mpDirectionsService = MPDirectionsService(mContext)
        mpDirectionsService?.setRouteResultListener(this::onRouteResult)
    }
    mpDirectionsService?.setTravelMode(MPTravelMode.WALKING)
    //Queries the MPDirectionsService for a route with the hardcoded user location and the point from a location.
    mpDirectionsService?.query(mUserLocation, mpLocation.point)
}
```

The travel modes generally only apply for outdoor navigation. Indoor navigation calculations are based on the **walking** travel mode.

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions-1) <a href="#route-restrictions-1" id="route-restrictions-1"></a>

#### Avoiding Stairs and Steps[​](https://docs.mapsindoors.com/directions-service#avoiding-stairs-and-steps-1) <a href="#avoiding-stairs-and-steps-1" id="avoiding-stairs-and-steps-1"></a>

For wheelchair users or other users with limited mobility, it may be relevant to request a Route that avoids stairs and steps. Avoid certain **way types** on the route using the `addRouteRestriction` method on `MPDirectionsService`.

```kotlin
fun getRoute() {
  val directionsService = MPDirectionsService(mContext)
  val directionsRenderer = MPDirectionsRenderer(mMapControl)
  val origin = MPPoint(57.057917, 9.950361, 0.0)
  val destination = MPPoint(57.058038, 9.950509, 0.0)
  directionsService.addAvoidWayType(MPHighway.STEPS)
  directionsService.addAvoidWayType(MPHighway.ELEVATOR)
  directionsService.setRouteResultListener { route, error -> }
  directionsService.query(origin, destination)
}
```

When Route restrictions are set on the `MPDirectionsService` they will be applied to any subsequent queries as well. You can remove them again by calling `clearRouteRestrictions`.

```kotlin
fun clearRouteRestrictions() {
    mpDirectionsService?.clearRouteRestrictions()
}
```

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions-1) <a href="#app-user-role-restrictions-1" id="app-user-role-restrictions-1"></a>

In the MapsIndoors CMS it is possible to restrict certain **ways** in the Route Network to only be accessible by users belonging to certain Roles.

You can get the available Roles with help of the `MapsIndoors.getAppliedUserRoles`:

```kotlin
fun getUserRoles(): List<MPUserRole>? {
  return MapsIndoors.getAppliedUserRoles()
}
```

User Roles can be set on a global level using `MapsIndoors.applyUserRoles`.

```kotlin
fun setUserRoles(userRoles: List<MPUserRole>) {
    MapsIndoors.applyUserRoles(userRoles)
}
```

This will affect all following Directions requests as well as search queries with `MapsIndoors`.

For more information about App User Roles, see [this documentation](https://docs.mapsindoors.com/app-user-roles/).

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time-1) <a href="#transit-departure-and-arrival-time-1" id="transit-departure-and-arrival-time-1"></a>

When using the Transit travel mode, you must set a **departure date** or an **arrival date** on the route using the `setTime` method on `MPDirectionsService` and declaring if it is a departure or not through `setIsDeparture`. The `date` parameter is the epoch time, in seconds, as an integer, and it is only possible to use one of these properties at a time.

```kotlin
fun setDepartureTime(date: Date?) {
    mpDirectionsService.setIsDeparture(true)
    mpDirectionsService.setTime(date)
}
fun setArrivalTime(date: Date?) {
    mpDirectionsService.setIsDeparture(false)
    mpDirectionsService.setTime(date)
}
```
{% endtab %}
{% endtabs %}
