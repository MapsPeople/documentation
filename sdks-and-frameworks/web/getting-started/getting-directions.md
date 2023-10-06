# Getting Directions

In this step you'll create directions between two points and change the transportation mode.

### Get Directions Between Two Locations[​](https://docs.mapsindoors.com/getting-started/web/directions#get-directions-between-two-locations) <a href="#get-directions-between-two-locations" id="get-directions-between-two-locations"></a>

To get directions between two MapsIndoors Locations, or places outside of your MapsIndoors solution, we need two things:

1. The Directions Service instance
2. The Directions Render instance

We need the Directions Service to calculate the fastest route between two points, and use the Directions Render to actually draw the route on the map.



{% tabs %}
{% tab title="Google Maps - Manually" %}
#### Get Directions Service and Render instances <a href="#get-directions-service-and-render-instances" id="get-directions-service-and-render-instances"></a>

First, initialize the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html), and add an external Directions Provider (in this case Google Maps).

Then, we need to initialize the [MapsIndoors Directions Render](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html) with the MapsIndoors instance:

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const googleMapsInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.GoogleMapsProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
googleMapsInstance.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelectorElement);

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}
```

> See all available directions render options and methods in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

Now our app is ready to provide directions. Next up is how to give it an Origin and Destination _-_ and draw a route between those.

#### Draw a Route on the Map[​](https://docs.mapsindoors.com/getting-started/web/directions#draw-a-route-on-the-map) <a href="#draw-a-route-on-the-map" id="draw-a-route-on-the-map"></a>

To display a route on the map, we use the coordinates of an Origin and Destination and draw a line between them. For this, we use MapsIndoors' [DirectionsRenderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

The Destination coordinate is retrieved dynamically using the coordinate of the selected Location in the search results list. Therefore, you must search for the destination to get directions, and then click the result in the text list. Different solutions can of course be implemented into your own solution later.&#x20;

In this tutorial, the Origin is, naturally, a hardcoded coordinate in the demo API key supplied with this guide. If you're using you own key, you can hardcode coordinates from a Location in your building instead.

In the following example, this is what happens:

1. Create a new `getRoute` method in `main.js` which accepts a location
2. Create two new constants, one for the Origin's coordinate, and another for the Destination's coordinate
3. Add another constant defining the `routeParameters`
4.  Using the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute) call the `getRoute` method to get the fastest route between the two coordinates

    > See all available route parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute).
5. Using the [MapsIndoors Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html#setRoute) call the `setRoute` method to display the route on the map



```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const googleMapsInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.GoogleMapsProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
googleMapsInstance.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelectorElement);

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now, to make it more dynamic, we attach a `click` event listener for each location appended to the search results list element with the `getRoute` method as a callback function.

You will now have something like this:

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const googleMapsInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.GoogleMapsProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
googleMapsInstance.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelectorElement);

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;

    // Add click event listener
    listElement.addEventListener("click", () => getRoute(location), false);

      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now you can click on any item in the search results list to get directions from the hardcoded origin to that destination.

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/web/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as travel mode. There are four travel modes; walking, bicycling, driving and transit (public transportation). The travel modes apply for outdoor navigation. Indoor navigation calculations are based on walking travel mode.

To change between travel modes we first need to add a `<select>` element with all four transportation options above the search field:

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.4/mapsindoors-4.21.4.js.gz?apikey=d876ff0e60bb430b8fabb145"></script>
    <script src="https://maps.googleapis.com/maps/api/js?libraries=geometry"></script>
</head>
<body>
  <div id="map" style="width: 600px; height: 600px;"></div>

<!-- Travel mode selector -->
<label for="travel-modes">Choose a travel mode:</label>
<select name="travelModeSelector" id="travel-modes">
  <option value="walking" selected>Walking</option>
  <option value="bicycling">Bicycling</option>
  <option value="driving">Driving</option>
  <option value="transit">Transit</option>
</select>

  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>

  <ul id="search-results"></ul>
</body>
</html>
```

To use the chosen travel mode when getting a route, we need to replace the hardcoded value for `travelMode` parameter inside the `getRoute` method with the `<select>` elements value:

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const googleMapsInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.GoogleMapsProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
googleMapsInstance.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelectorElement);

function onSearch() {
  const searchInputElement = document.querySelector('input');
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      // Add click event listener
      listElement.addEventListener("click", () => getRoute(location), false);
      searchResultsElement.appendChild(listElement);
    });
    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
  travelMode: document.getElementById('travel-modes').value.toUpperCase()
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

You now have something like this:

{% embed url="https://jsfiddle.net/mapspeople/ek48xcLg/1/" %}
{% endtab %}

{% tab title="Google Maps - MI Components" %}
#### Get Directions Service and Render instances <a href="#get-directions-service-and-render-instances" id="get-directions-service-and-render-instances"></a>

First, add two new `let` statements all the way at the top, after the `miMapElement` constant for storing our Directions Service and Directions Renderer instances. Then, we populate them with the instances within the `mapsIndoorsReady` event:

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
miMapElement.getDirectionsServiceInstance()
  .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

miMapElement.getDirectionsRendererInstance()
  .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});
```

> See all available directions render options and methods in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

Now our app is ready to provide directions. Next up is how to give it an Origin and Destination - and draw a route between those.

#### Draw a Route on the Map[​](https://docs.mapsindoors.com/getting-started/web/directions#draw-a-route-on-the-map) <a href="#draw-a-route-on-the-map" id="draw-a-route-on-the-map"></a>

Now our app is ready to provide directions. Next up is how to give it an Origin and Destination _-_ and draw a route between those.

#### Draw a Route on the Map[​](https://docs.mapsindoors.com/getting-started/web/directions#draw-a-route-on-the-map) <a href="#draw-a-route-on-the-map" id="draw-a-route-on-the-map"></a>

To display a route on the map, we use the coordinates of an Origin and Destination and draw a line between them. For this, we use MapsIndoors' [DirectionsRenderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

The Destination coordinate is retrieved dynamically using the coordinate of the selected Location in the search results list. Therefore, you must search for the destination to get directions, and then click the result in the text list. Different solutions can of course be implemented into your own solution later.&#x20;

In this tutorial, the Origin is, naturally, a hardcoded coordinate in the demo API key supplied with this guide. If you're using you own key, you can hardcode coordinates from a Location in your building instead.

In the following example, this is what happens:

1. Create a new `getRoute` method in `main.js` which accepts a `location`
2. Create two new constants, one for the Origin's coordinate, and another for the Destination's coordinate
3. Add another constant defining the `routeParameters`
4.  Using the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute) call the `getRoute` method to get the fastest route between the two coordinates

    > See all available route parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute).
5. Using the [MapsIndoors Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html#setRoute) call the `setRoute` method to display the route on the map

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
  miMapElement.getDirectionsServiceInstance()
    .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance()
    .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now, to make it more dynamic, we attach a `click` event listener for each location appended to the search results list element with the `getRoute` method as a callback function.

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
  miMapElement.getDirectionsServiceInstance()
    .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance()
    .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;

  // Add click event listener
  miListItemElement.addEventListener("click", () => getRoute(location), false);

    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now you can click on any item in the search results list to get directions from the hardcoded origin to that destination.

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/web/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as travel mode. There are four travel modes; walking, bicycling, driving and transit (public transportation). The travel modes apply for outdoor navigation. Indoor navigation calculations are based on walking travel mode.

To change between travel modes we first need to add a `<select>` element with all four transportation options above the search field:

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
    <script src="https://unpkg.com/@mapsindoors/components@8.2.0/dist/mi-components/mi-components.js"></script>
</head>
<body>
  <mi-map-googlemaps
    style="width: 600px; height: 600px;"
    mi-api-key="d876ff0e60bb430b8fabb145"
    floor-selector-control-position="TOP_RIGHT">
  </mi-map-googlemaps>

<!-- Travel mode selector -->
<label for="travel-modes">Choose a travel mode:</label>
<select name="travelModeSelector" id="travel-modes">
  <option value="walking" selected>Walking</option>
  <option value="bicycling">Bicycling</option>
  <option value="driving">Driving</option>
  <option value="transit">Transit</option>
</select>

  <mi-search
    style="width: 600px;"
    mapsindoors="true"
    placeholder="Search">
  </mi-search>

  <mi-list
    style="width: 600px; height: 400px;"
    scroll-buttons-enabled="true"
    scroll-length="200">
  </mi-list>
</body>
</html>
```

To use the chosen travel mode when getting a route, we need to replace the hardcoded value for `travelMode` parameter inside the `getRoute` method with the `<select>` elements value:

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });

  miMapElement.getDirectionsServiceInstance().then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance().then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    // Add click event listener
    miListItemElement.addEventListener("click", () => getRoute(location), false);
    miListElement.appendChild(miListItemElement);
  });
  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
  });
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
  travelMode: document.getElementById('travel-modes').value.toUpperCase()
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```



You now have something like this:

{% embed url="https://jsfiddle.net/mapspeople/jzqx7kmy/1/" %}
{% endtab %}

{% tab title="Mapbox - Manually" %}
#### Get Directions Service and Render instances <a href="#get-directions-service-and-render-instances" id="get-directions-service-and-render-instances"></a>

First, initialize the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html), and add an external Directions Provider (in this case Mapbox).

Then, we need to initialize the [MapsIndoors Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html) with the MapsIndoors instance:

```javascript
// main.js

const mapViewOptions = {
  accessToken: "YOUR_MAPBOX_ACCESS_TOKEN",
  element: document.getElementById("map"),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const mapboxInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.MapboxProvider('YOUR_MAPBOX_ACCESS_TOKEN');
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}
```

> See all available directions render options and methods in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

Now our example app is ready to provide Directions. Next up is how to give it an Origin and Destination and draw the route between.

#### Draw a Route on the Map[​](https://docs.mapsindoors.com/getting-started/web/directions#draw-a-route-on-the-map) <a href="#draw-a-route-on-the-map" id="draw-a-route-on-the-map"></a>

To display a route on the map, we use the coordinates of an Origin and Destination and draw a line between them. For this, we use MapsIndoors' [Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

The Destination coordinate is retrieved dynamically, using the coordinate of the selected Location in the search results list. Therefore, you must search for the destination to get directions, and then click the result in the text list. Different solutions can of course be implemented into your own solution later.&#x20;

In this tutorial, the Origin is, naturally, a hardcoded coordinate in the demo API key supplied with this guide. If you're using you own key, you can hardcode coordinates from a Location in your building instead.

In the following example, this is what happens:

1. Create a new `getRoute` method in `main.js` which accepts a `location`
2. Create two new constants, one for the Origin's coordinate, and another for the Destination's coordinate
3. Add another constant defining the `routeParameters`
4.  Using the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute) call the `getRoute` method to get the fastest route between the two coordinates

    > See all available route parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute).
5. Using the [MapsIndoors Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html#setRoute) call the `setRoute` method to display the route on the map

```javascript
// main.js

const mapViewOptions = {
  accessToken: "YOUR_MAPBOX_ACCESS_TOKEN",
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const mapboxInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.MapboxProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now, to make it more dynamic, we attach a `click` event listener for each location appended to the search results list element with the `getRoute` method as a callback function.

```javascript
// main.js

const mapViewOptions = {
  accessToken: "YOUR_MAPBOX_ACCESS_TOKEN",
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const mapboxInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.MapboxProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });

function onSearch() {
  const searchInputElement = document.querySelector('input');
  // Get list element reference
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;

    // Add click event listener
    listElement.addEventListener("click", () => getRoute(location), false);

      searchResultsElement.appendChild(listElement);
    });

    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now you can click on each item in the search results list to get directions.

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/web/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as travel mode. There are four travel modes; walking, bicycling, driving and transit (public transportation). The travel modes apply for outdoor navigation. Indoor navigation calculations are based on walking travel mode.

To change between travel modes we first need to add a `<select>` element with all four transportation options above the search field:

```javascript
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.4/mapsindoors-4.21.4.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src='https://api.mapbox.com/mapbox-gl-js/v1.10.1/mapbox-gl.js'></script>
  <link href='https://api.mapbox.com/mapbox-gl-js/v1.10.1/mapbox-gl.css' rel='stylesheet' />
</head>
<body>
  <div id="map" style="width: 600px; height: 600px;"></div>

<!-- Travel mode selector -->
<label for="travel-modes">Choose a travel mode:</label>
<select name="travelModeSelector" id="travel-modes">
  <option value="walking" selected>Walking</option>
  <option value="bicycling">Bicycling</option>
  <option value="driving">Driving</option>
  <option value="transit">Transit</option>
</select>

  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>

  <ul id="search-results"></ul>
</body>
</html>
```

To use the chosen transportation when getting a route, we need to replace the hardcoded value for `travelMode` parameter inside the `getRoute` method with the `<select>` elements value:

```javascript
// main.js

const mapViewOptions = {
  accessToken: "YOUR_MAPBOX_ACCESS_TOKEN",
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });
const mapboxInstance = mapViewInstance.getMap();

const externalDirectionsProvider = new mapsindoors.directions.MapboxProvider();
const miDirectionsServiceInstance = new mapsindoors.services.DirectionsService(externalDirectionsProvider);
const directionsRendererOptions = { mapsIndoors: mapsIndoorsInstance }
const miDirectionsRendererInstance = new mapsindoors.directions.DirectionsRenderer(directionsRendererOptions);

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });

function onSearch() {
  const searchInputElement = document.querySelector('input');
  const searchResultsElement = document.getElementById('search-results');

  const searchParameters = { q: searchInputElement.value };
  mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
    // Reset search results list
    searchResultsElement.innerHTML = null;

    // Append new search results
    locations.forEach(location => {
      const listElement = document.createElement('li');
      listElement.innerHTML = location.properties.name;
      // Add click event listener
      listElement.addEventListener("click", () => getRoute(location), false);
      searchResultsElement.appendChild(listElement);
    });
    // Filter map to only display search results
    mapsIndoorsInstance.filter(locations.map(location => location.id), false);
  });
}

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
  travelMode: document.getElementById('travel-modes').value.toUpperCase()
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

You now have something like this:

{% embed url="https://jsfiddle.net/mapspeople/z23vjhf4/1/" %}
{% endtab %}

{% tab title="Mapbox - MI Components" %}
#### Get Directions Service and Render instances <a href="#get-directions-service-and-render-instances" id="get-directions-service-and-render-instances"></a>

First, add two new `let` statements all the way at the top, after the `miMapElement` constant for storing our Directions Service and Directions Renderer instances. Then, we populate them with the instances within the `mapsIndoorsReady` event:

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-mapbox');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
miMapElement.getDirectionsServiceInstance()
  .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

miMapElement.getDirectionsRendererInstance()
  .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})
miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});
```

> See all available directions render options and methods in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

Now our app is ready to provide directions. Next up is how to give it an Origin and Destination - and draw a route between those.

#### Draw a Route on the Map[​](https://docs.mapsindoors.com/getting-started/web/directions#draw-a-route-on-the-map) <a href="#draw-a-route-on-the-map" id="draw-a-route-on-the-map"></a>

To display a route on the map, we use the coordinates of an Origin and Destination and draw a line between them. For this, we use MapsIndoors' [Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html).

The Destination coordinate is retrieved dynamically, using the coordinate of the selected Location in the search results list. Therefore, you must search for the destination to get directions, and then click the result in the text list. Different solutions can of course be implemented into your own solution later.&#x20;

In this tutorial, the Origin is, naturally, a hardcoded coordinate in the demo API key supplied with this guide. If you're using you own key, you can hardcode coordinates from a Location in your building instead.

In the following example, this is what happens:

1. Create a new `getRoute` method in `main.js` which accepts a `location`
2. Create two new constants, one for the Origin's coordinate, and another for the Destination's coordinate
3. Add another constant defining the `routeParameters`
4.  Using the [MapsIndoors Directions Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute) call the `getRoute` method to get the fastest route between the two coordinates

    > See all available route parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.DirectionsService.html#getRoute).
5. Using the [MapsIndoors Directions Renderer](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.directions.DirectionsRenderer.html#setRoute) call the `setRoute` method to display the route on the map

```javascript
// main.js

const miMapElement = document.querySelector("mi-map-mapbox");
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
  miMapElement.getDirectionsServiceInstance()
    .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance()
    .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now, to make it more dynamic, we attach a `click` event listener for each location appended to the search results list element with the `getRoute` method as a callback function.

```javascript
// main.js

const miMapElement = document.querySelector("mi-map-mapbox");
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
      mapInstance.setCenter([-77.0362723, 38.8974905]); // The White House
  });
  miMapElement.getDirectionsServiceInstance()
    .then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance()
    .then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;

  // Add click event listener
  miListItemElement.addEventListener("click", () => getRoute(location), false);

    miListElement.appendChild(miListItemElement);
  });

  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
    travelMode: 'WALKING'
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

Now you can click on any item in the search results list to get directions from the hardcoded origin to that destination.

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/web/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as travel mode. There are four travel modes; walking, bicycling, driving and transit (public transportation). The travel modes apply for outdoor navigation. Indoor navigation calculations are based on walking travel mode.

To change between travel modes we first need to add a `<select>` element with all four transportation options above the search field:

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
    <script src="https://unpkg.com/@mapsindoors/components@8.2.0/dist/mi-components/mi-components.js"></script>
</head>
<body>
  <mi-map-mapbox
    access-token="YOUR_MAPBOX_ACCESS_TOKEN"
    style="width: 600px; height: 600px;"
    mi-api-key="d876ff0e60bb430b8fabb145"
    floor-selector-control-position="TOP_RIGHT">
  </mi-map-mapbox>

<!-- Travel mode selector -->
<label for="travel-modes">Choose a travel mode:</label>
<select name="travelModeSelector" id="travel-modes">
  <option value="walking" selected>Walking</option>
  <option value="bicycling">Bicycling</option>
  <option value="driving">Driving</option>
  <option value="transit">Transit</option>
</select>

  <mi-search
    style="width: 600px;"
    mapsindoors="true"
    placeholder="Search">
  </mi-search>

  <mi-list
    style="width: 600px; height: 400px;"
    scroll-buttons-enabled="true"
    scroll-length="200">
  </mi-list>
</body>
</html>
```

To use the chosen travel mode when getting a route, we need to replace the hardcoded value for `travelMode` parameter inside the `getRoute` method with the `<select>` elements value:

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-mapbox');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

let miDirectionsServiceInstance;
let miDirectionsRendererInstance;

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });

  miMapElement.getDirectionsServiceInstance().then((directionsServiceInstance) => miDirectionsServiceInstance = directionsServiceInstance);

  miMapElement.getDirectionsRendererInstance().then((directionsRendererInstance) => miDirectionsRendererInstance = directionsRendererInstance);
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;

  // Append new search results
  event.detail.forEach(location => {
    const miListItemElement = document.createElement('mi-list-item-location');
    miListItemElement.location = location;
    // Add click event listener
    miListItemElement.addEventListener("click", () => getRoute(location), false);
    miListElement.appendChild(miListItemElement);
  });
  // Get the MapsIndoors instance
  miMapElement.getMapsIndoorsInstance().then((mapsIndoorsInstance) => {
    // Filter map to only display search results
    mapsIndoorsInstance.filter(event.detail.map(location => location.id), false);
  });
});

function getRoute(location) {
  const originLocationCoordinate = { lat: 38.897389429704695, lng: -77.03740973527613, floor: 0 }; // Oval Office, The White House (Hardcoded coordinate and floor index)
  const destinationCoordinate = { lat: location.properties.anchor.coordinates[1], lng: location.properties.anchor.coordinates[0], floor: location.properties.floor };

  // Route parameters
  const routeParameters = {
    origin: originLocationCoordinate,
    destination: destinationCoordinate,
  travelMode: document.getElementById('travel-modes').value.toUpperCase()
  };

  // Get route from directions service
  miDirectionsServiceInstance.getRoute(routeParameters).then((directionsResult) => {
    // Use directions render to display route
    miDirectionsRendererInstance.setRoute(directionsResult);
  });
}
```

You now have something like this:

{% embed url="https://jsfiddle.net/mapspeople/g4epm0zj/1/" %}
{% endtab %}
{% endtabs %}
