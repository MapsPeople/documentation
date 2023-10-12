---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# Directions Service

<mark style="background-color:red;">Introduction missing</mark>

<mark style="background-color:blue;">MJE: The first two lines below works for me as intro.</mark>

The class `DirectionsService` is used to request routes from one point to another. The minimal required input is an `origin` and a `destination`.&#x20;

This example shows how to set up and execute a query for a Route:

<mark style="background-color:blue;">MJE: We need sample code for Mapbox in a separate tap! DOC-84 oprettet.</mark>

```
const externalDirectionsProvider = new mapsindoors.directions.GoogleMapsProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);

const routeParameters = {
  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }, // Oval Office, The White House
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 } // Blue Room, The White House
};

miDirectionsServiceInstance.getRoute(routeParameters).then(directionsResult => {
  console.log(directionsResult);
});
```

> For more information, see the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html).

### Change Transportation Mode[​](https://docs.mapsindoors.com/directions-service#change-transportation-mode-3) <a href="#change-transportation-mode-3" id="change-transportation-mode-3"></a>

In MapsIndoors, the transportation mode is referred to as travel mode. There are four travel modes, walking, bicycling, driving and transit (public transportation). The travel modes generally apply to outdoor navigation. Indoor navigation calculations are based on walking travel mode.

Set travel mode on your request using the `travelMode` property on `routeParameters`:

```
const routeParameters = {
  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }, // Oval Office, The White House
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 }, // Blue Room, The White House
  travelMode: 'WALKING'
};
```

> Please note that not all map engines support public transportation as travel mode. (<mark style="background-color:red;">Relevance? Er det Mapbox?</mark>) <mark style="background-color:blue;">MJE: Ja, er man på Mapbox så findes dén mulighed ikke. Måske vi bare skal skrive det sådan; Please note that with Mapbox, transit is not an option.</mark>

### Route Restrictions[​](https://docs.mapsindoors.com/directions-service#route-restrictions-3) <a href="#route-restrictions-3" id="route-restrictions-3"></a>

#### Avoiding Stairs and Steps[​](https://docs.mapsindoors.com/directions-service#avoiding-stairs-and-steps-3) <a href="#avoiding-stairs-and-steps-3" id="avoiding-stairs-and-steps-3"></a>

For a wheelchair user or a user with physical disabilities it could be relevant to request a Route that avoids stairs, escalators and steps.

Set avoid stairs on your request using the `avoidStairs` property on `routeParameters`:

```
const routeParameters = {
  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }, // Oval Office, The White House
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 }, // Blue Room, The White House
  avoidStairs: 'true'
};
```

#### App User Role Restrictions[​](https://docs.mapsindoors.com/directions-service#app-user-role-restrictions-3) <a href="#app-user-role-restrictions-3" id="app-user-role-restrictions-3"></a>

<mark style="background-color:red;">Skulle vi starte med en enkel sætning introducing User Roles og så linke til den overordnede user roles article?</mark> <mark style="background-color:blue;">JA:</mark>

<mark style="background-color:blue;">Application User Roles is a feature that lets you define various roles, you can assign to your users. In the context of route calculation, the feature is used to differentiate routing from one user type to the another.</mark> In the MapsIndoors CMS it is possible to restrict paths and doors in the route network for certain User Roles.<mark style="background-color:blue;">\[LINK HERE]</mark>



You can get available Roles for your MapsIndoors Solution with the help of the `SolutionsService`: <mark style="background-color:red;">(<- Er det korrekt? Mener vi "You can see/get the roles available"? Eller hvad?)</mark> <mark style="background-color:blue;">Yes, har omskrevet lidt ovenfor</mark>

```
mapsindoors.services.SolutionsService.getUserRoles().then(userRoles => {
  console.log(userRoles);
});
```

> For more information, see the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.SolutionsService.html#getUserRoles).

User Roles can be set on a global level using `mapsindoors.MapsIndoors.setUserRoles()`.

```
mapsindoors.MapsIndoors.setUserRoles(['myUserRoleId']);
```

> For more information, see the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#.setUserRoles).

This will affect all following Directions requests, visibility of Locations as well as search queries with `LocationsService`.

### Transit Departure and Arrival Time[​](https://docs.mapsindoors.com/directions-service#transit-departure-and-arrival-time-3) <a href="#transit-departure-and-arrival-time-3" id="transit-departure-and-arrival-time-3"></a>

Set a departure date or an arrival date on the route using the `transitOptions` property. It will only make sense to set one of these properties at a time. <mark style="background-color:red;">Why?</mark> <mark style="background-color:blue;">Maja, because either you specify</mark> <mark style="background-color:blue;"></mark>_<mark style="background-color:blue;">when</mark>_ <mark style="background-color:blue;"></mark><mark style="background-color:blue;">you start your journey - or</mark> <mark style="background-color:blue;"></mark>_<mark style="background-color:blue;">when</mark>_ <mark style="background-color:blue;"></mark><mark style="background-color:blue;">you want to arrive.</mark>&#x20;

<mark style="background-color:blue;">MJE: This parameter is only implemented on our side with Google Maps, not Mapbox.</mark>

```
const departureDate = new Date(new Date().getTime() + 30*60000); // 30 minutes from now

const routeParameters = {
  origin: { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }, // Oval Office, The White House
  destination: { lat: 38.897579747054046, lng: -77.03658652944773, floor: 1 }, // Blue Room, The White House
  travelMode: 'TRANSIT',
  transitOptions: {
    departureTime: departureDate
  }
};
```

> For more information about available options on the `transitOption` object, see [google.com/maps/documentation](https://developers.google.com/maps/documentation/javascript/reference/directions#TransitOptions.departureTime). <mark style="background-color:red;">Dette er vel kun for Google Maps? Skal vi slette den eller tilføje en tilsvarende, hvis muligt, om Mapbox?</mark>&#x20;
