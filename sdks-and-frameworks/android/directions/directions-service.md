---
description: Android v4
---

# Directions Service

The class `MPDirectionsService` is used to request routes from one point to another. The minimum required input to receive a route is an `origin` and a `destination`.&#x20;

This example shows how to setup and execute a query for a Route:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val directionsService = MPDirectionsService()
val directionsRenderer = MPDirectionsRenderer(mapControl)
val origin = MPPoint(57.057917, 9.950361, 0.0)
val destination = MPPoint(57.058038, 9.950509, 0.0)
directionsService.setRouteResultListener { route, error -> }
directionsService.query(origin, destination)
```
{% endtab %}

{% tab title="Java" %}
```java
MPDirectionsService directionsService = new MPDirectionsService();
MPDirectionsRenderer directionsRenderer = new MPDirectionsRenderer(mapControl);
MPPoint origin = new MPPoint(57.057917, 9.950361, 0.0);
MPPoint destination = new MPPoint(57.058038, 9.950509, 0.0);
directionsService.setRouteResultListener((route, error) -> {
});
directionsService.query(origin, destination);
```
{% endtab %}
{% endtabs %}

The route can be customized via the `directionsRenderer`. An example could be the color of the rendered path and the background color of the rendered line. This can be set like this:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val directionsRenderer = MPDirectionsRenderer(mapControl)
directionsRenderer.setPolylineColors(Color.GREEN, Color.BLACK)
```
{% endtab %}

{% tab title="Java" %}
```java
MPDirectionsRenderer directionsRenderer = MPDirectionsRenderer(mapControl);
directionsRenderer.setPolylineColors(Color.GREEN, Color.BLACK);
```
{% endtab %}
{% endtabs %}

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

Set the **travel mode** on your request using the `setTravelMode` method on `MPDirectionsService`:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun createRoute(mpLocation: MPLocation) {
    // if MPDirectionsService has not been instantiated create it here and assign the results call back to the activity.
    if (mpDirectionsService == null) {
        mpDirectionsService = MPDirectionsService()
        mpDirectionsService?.setRouteResultListener(this::onRouteResult)
    }
    mpDirectionsService?.setTravelMode(MPTravelMode.WALKING)
    // queries the MPDirectionsService for a route with the hardcoded user location and the point from a location.
    mpDirectionsService?.query(mUserLocation, mpLocation.point)
}
```
{% endtab %}

{% tab title="Java" %}
```java
void createRoute(MPLocation mpLocation) {
    // if MPDirectionsService has not been instantiated create it here and assign the results call back to the activity.
    if (mpDirectionsService == null) {
        mpDirectionsService = new MPDirectionsService();
        mpDirectionsService.setRouteResultListener(this::onRouteResult);
    }
    mpDirectionsService.setTravelMode(MPTravelMode.WALKING);
    // queries the MPDirectionsService for a route with the hardcoded user location and the point from a location.
    mpDirectionsService.query(mUserLocation, mpLocation.getPoint());
}
```
{% endtab %}
{% endtabs %}

The travel modes generally only apply for outdoor navigation. Indoor navigation calculations are based on the **walking** travel mode.

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions-2) <a href="#route-restrictions-2" id="route-restrictions-2"></a>

There are several ways to influence the calculated route by applying various restrictions to the directions query:

* **Avoid** and/or **exclude** certain **way types** under certain circumstances
* Apply restrictions based on User Roles

#### Avoid and exclude way types <a href="#route-restrictions-2" id="route-restrictions-2"></a>

It is possible to avoid and/or exclude certain way types when calculating a route. **Avoiding** a way type means that `DirectionsService` will do its best to find a route without the avoided way types, but if no route can be found without them, it will try to find a route where the avoided way types may be used. To eliminate certain way types entirely, add them as **excluded** way types. If a way type is both avoided and excluded, excluded will be obeyed.

It may for example be desirable to provide a route better suited for users with physical disabilities by avoiding e.g. stairs. This can be achieved by avoiding that **way type** on the route using the `avoidWayTypes` property:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.addAvoidWayType(MPHighway.STEPS)
```
{% endtab %}

{% tab title="Java" %}
```java
MPDirectionsService directionsService = new MPDirectionsService();
directionsService.addAvoidWayType(MPHighway.STEPS);
```
{% endtab %}
{% endtabs %}

In an emergency situation it may be required to not use elevators at all. This can be achieved by adding the way type elevator to the `excludeWayTypes` property:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.addExcludeWayType(MPHighway.ELEVATOR)
```
{% endtab %}

{% tab title="Java" %}
```java
MPDirectionsService directionsService = new MPDirectionsService();
directionsService.addExcludeWayType(MPHighway.ELEVATOR);
```
{% endtab %}
{% endtabs %}

When Route restrictions are set on the `MPDirectionsService` they will be applied to any subsequent queries as well. You can remove them again by calling `clearAvoidWayType` or `clearExcludeWayType`.

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val directionsService: MPDirectionsService = MPDirectionsService()
directionsService.clearAvoidWayType()
directionsService.clearExcludeWayType()
```
{% endtab %}

{% tab title="Java" %}
```java
MPDirectionsService directionsService = new MPDirectionsService();
directionsService.clearAvoidWayType();
directionsService.clearExcludeWayType();
```
{% endtab %}
{% endtabs %}

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions) <a href="#app-user-role-restrictions" id="app-user-role-restrictions"></a>

In the MapsIndoors CMS it is possible to restrict certain **ways** in the Route Network to only be accessible by users belonging to certain Roles.

You can get the available Roles with help of the `MapsIndoors.getAppliedUserRoles`:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
fun getUserRoles(): List<MPUserRole>? {
  return MapsIndoors.getAppliedUserRoles()
}
```
{% endtab %}

{% tab title="Java" %}
```java
List<MPUserRole> getUserRoles() {
  return MapsIndoors.getAppliedUserRoles();
}
```
{% endtab %}
{% endtabs %}

User Roles can be set on a global level using `MapsIndoors.applyUserRoles`.

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
 fun setUserRoles(userRoles: List<MPUserRole>) {
    MapsIndoors.applyUserRoles(userRoles)
}
```
{% endtab %}

{% tab title="Java" %}
```java
void setUserRoles(List<MPUserRole> userRoles) {
    MapsIndoors.applyUserRoles(userRoles);
}
```
{% endtab %}
{% endtabs %}

This will affect all following Directions requests as well as search queries with `MapsIndoors`.

For more information about App User Roles, see [this documentation](https://docs.mapsindoors.com/app-user-roles/).

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time) <a href="#transit-departure-and-arrival-time" id="transit-departure-and-arrival-time"></a>

When using the Transit travel mode, you must set a **departure date** or an **arrival date** on the route using the `setTime` method on `MPDirectionsService` and declaring if it is a departure or not through `setIsDeparture`. The `date` parameter is the epoch time, in seconds, as an integer, and it is only possible to use one of these properties at a time.

{% tabs %}
{% tab title="Kotlin" %}
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

{% tab title="Java" %}
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
{% endtabs %}
