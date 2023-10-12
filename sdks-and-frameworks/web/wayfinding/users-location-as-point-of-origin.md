# User's Location as Point of Origin

<mark style="background-color:red;">Intro about the blue dot experience</mark>

Often you may want to get directions starting from a user's actual current position instead of from another fixed Location. The following code snippet gives an example on how to implement this.

```javascript
// Setting styling options to the route path.
directionsRenderer.setOptions({
    strokeColor: '#bada55',
    strokeWeight: 10,
});
// Latitude and longitude position of the route destination.
const routeDestination = {
    lat: 57.058230237700194,
    lng: 9.951134229974498
};
// enableHighAccuracy is a boolean value that indicates that the application would like to receive the best possible results.
// timeout represents the maximum length of time (milliseconds) the device is allowed to take in order to return a position.
const options = {
    enableHighAccuracy: true,
    timeout: 5000
}
// Creates origin with the users latitude and longitude. Then sets the route from this position to the route destination.
function getRoute(pos) {
    const crd = pos.coords;
    const origin = {
        lat: crd.latitude,
        lng: crd.longitude
    }
    directionsService.getRoute({ origin: origin, destination: routeDestination }).then(function (res) {
        if (res === undefined) {
            console.log('Error: Route is undefined.')
        } else {
            console.log('Route', res);
            directionsRenderer.setRoute(res);
        }
    });
}
// Error handling function.
function errorHandler(err) {
    console.log('Error: ', err)
}
// Gets users device current position and sets route with additional options. If any errors, errorHandler will trigger.
navigator.geolocation.getCurrentPosition(getRoute, errorHandler, options);
```

Further details on how user positioning works, and how to display it, can be found [here](https://docs.mapsindoors.com/blue-dot/). <mark style="background-color:blue;">MJE: Link needs an update to the right page on the new site.</mark>

This results in directions queries originating from the user's current location.
