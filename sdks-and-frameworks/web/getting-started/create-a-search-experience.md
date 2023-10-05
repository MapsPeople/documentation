# Create a Search Experience

In this step, you'll create a simple search and display the search results in a list. You'll also learn how to filter the data displayed on the map.

{% tabs %}
{% tab title="Google Maps - Manually" %}
## Create a Simple Query Search <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

MapsIndoors Locations can be retrieved in the MapsIndoors namespace using the [`LocationsService.getLocations()` method](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations) but first you need to add a `<input>` and `<button>` element to the DOM.

* Create an `<input>` and `<button>` element in `<body>`.
* Attach an `onclick` event to the `<button>` element and call a `onSearch` method, which you will create next.

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.4/mapsindoors-4.21.4.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src="https://maps.googleapis.com/maps/api/js?libraries=geometry&key=YOUR_GOOGLE_MAPS_API_KEY"></script>
</head>
<body>
  <div id="map" style="width: 600px; height: 600px;"></div>
  <script src="main.js"></script>
  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>
</body>
</html>
```

* Create the `onSearch` method.
* Get a reference to the search `<input>` element.
* Define a new object with the search parameter `q` and the value of `searchInputElement`.
* Call the `getLocations` method and log out the results to the console.

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

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
googleMapsInstance.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelectorElement);

  function onSearch() {
    const searchInputElement = document.querySelector('input');

    const searchParameters = { q: searchInputElement.value };
    mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
      console.log(locations);
    });
  }
```

See all available search parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations).

### Display a List of Search Results <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

To display a list of search results you can append each search result to a list element.

* Add the `<ul>` list element below the search field in `<body>` with the `id` attribute set to "search-results".

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.21.4/mapsindoors-4.21.4.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src="https://maps.googleapis.com/maps/api/js?libraries=geometry&key=YOUR_GOOGLE_MAPS_API_KEY"></script>
</head>
<body>
  <div id="map" style="width: 600px; height: 600px;"></div>
  <script src="main.js"></script>
  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>
  <ul id="search-results"></ul>
</body>
</html>
```

* Get a reference to the list element.
* Reset the list on every complete search.

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
  });
}
```

* Add a _for_ loop and append every result to the search results list element.

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
  });
}
```

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

To filter the map to only display the search results you can use the `filter` method.

* Call `mapsIndoorsInstance.filter` with an array of Location IDs.

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
}
```

To remove the location filter again, call `mapsIndoorsInstance.filter(null)`.

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/cwg9eumd/2/" %}


{% endtab %}

{% tab title="Google Maps - MI Components" %}
## Create a Simple Query Search <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

Using the `<mi-search>` component you get a `<input>`element tied tightly together with the [Location Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/LocationsService.html).

* Insert the `<mi-search>` custom element into `<body>`.
* Add the `mapsindoors` and `placeholder` attributes.

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
  <mi-map-googlemaps style="width: 600px; height: 600px;" gm-api-key="YOUR_GOOGLE_MAPS_API_KEY" mi-api-key="YOUR_MAPSINDOORS_API_KEY" floor-selector-control-position="TOP_RIGHT">
  </mi-map-googlemaps>
  <mi-search
  style="width: 600px;"
  mapsindoors="true"
  placeholder="Search">
  </mi-search>
  <script src="main.js"></script>
</body>
</html>
```

* Get a reference to the `<mi-search>` element.
* Attach an `results` event listener and log out the results to the console.

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
})

miSearchElement.addEventListener('results', (event) => {
    console.log(event.detail);
});
```

For more information on available events and how to configure the `<mi-search>` component, see [components.mapsindoors.com/search](https://components.mapsindoors.com/search/).

### Display a List of Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#show-a-list-of-search-results) <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

To display a list of search results you can append each search result to a list element.



* Insert the `<mi-list>` custom element below the search field in `<body>`.
* Add the `scroll-buttons-enabled` and `scroll-length` attributes.

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
  <mi-map-googlemaps style="width: 600px; height: 600px;" gm-api-key="YOUR_GOOGLE_MAPS_API_KEY" mi-api-key="YOUR_MAPSINDOORS_API_KEY" floor-selector-control-position="TOP_RIGHT">
  </mi-map-googlemaps>
  <mi-search style="width: 600px;" mapsindoors="true" placeholder="Search">
  </mi-search>
<mi-list
  style="width: 600px; height: 400px;"
  scroll-buttons-enabled="true"
  scroll-length="200">
</mi-list>
  <script src="main.js"></script>
</body>
</html>
```

For more information on how to configure the `<mi-list>` component, see [components.mapsindoors.com/list](https://components.mapsindoors.com/list/).

* Get a reference to the list element.
* Reset the list on every complete search.

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;
});
```

* Add a _for_ loop and append every result to the search results list element.

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
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
});
```

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

To filter the map to only display the search results you can use the `filter` method.



```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
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
}
```

To remove the location filter again, call `mapsIndoorsInstance.filter(null)`.

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/jtnw0u1y/1/" fullWidth="false" %}


{% endtab %}

{% tab title="Mapbox - Manually" %}
## Create a Simple Query Search <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

MapsIndoors Locations can be retrieved in the MapsIndoors namespace using the [`LocationsService.getLocations()` method](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations) but first you need to add an `<input>` and `<button>` element to the DOM.

* Create an `<input>` and `<button>` element in `<body>`.
* Attach an `onclick` event to the `<button>` element and call a `onSearch` method, which you will create next.

```html
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
  <script src="main.js"></script>
  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>
</body>
</html>
```

* Create the `onSearch` method.
* Get a reference to the search `<input>` element.
* Define a new object with the search parameter `q` and the value of `searchInputElement`.
* Call the `getLocations` method and log out the results to the console.

```javascript
// main.js

const mapViewOptions = {
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN',
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
    zoom: 17,
    maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({ mapView: mapViewInstance });

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });

  function onSearch() {
    const searchInputElement = document.querySelector('input');

    const searchParameters = { q: searchInputElement.value };
    mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
      console.log(locations);
    });
  }
```

See all available search parameters in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations).

### Display a List of Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#show-a-list-of-search-results) <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

To display a list of search results you can append each search result to a list element.

* Add the `<ul>` list element below the search field in `<body>` with the `id` attribute set to "search-results".

```html
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
  <script src="main.js"></script>
  <input type="text" placeholder="Search">
  <button onclick="onSearch()">Search</button>
  <ul id="search-results"></ul>
</body>
</html>
```

* Get a reference to the list element.
* Reset the list on every complete search.

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
  });
}
```

* Add a _for_ loop and append every result to the search results list element.

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
  });
}
```

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

To filter the map to only display the search results you can use the `filter` method.



* Call `mapsIndoorsInstance.filter` with an array of Location IDs.

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
}
```

To remove the location filter again, call `mapsIndoorsInstance.filter(null)`.

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/r86903om/" %}
{% endtab %}

{% tab title="Mapbox - MI Components" %}
## Create a Simple Query Search <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

Using the `<mi-search>` component you get an `<input>`element tied tightly together with the [Location Service](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/LocationsService.html).

* Insert the `<mi-search>` custom element into `<body>`.
* Add the `mapsindoors` and `placeholder` attributes.

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
  <mi-map-mapbox style="width: 600px; height: 600px;"
    access-token="YOUR_MAPBOX_ACCESS_TOKEN"
    mi-api-key="YOUR_MAPSINDOORS_API_KEY"
    floor-selector-control-position="TOP_RIGHT">
  </mi-map-mapbox>
<mi-search style="width: 600px;" mapsindoors="true" placeholder="Search">
</mi-search>
  <script src="main.js"></script>
</body>
</html>
```

* Get a reference to the `<mi-search>` element.
* Attach a `results` event listener and log the results in the console.

```javascript
// main.js
const miMapElement = document.querySelector('mi-map-mapbox');
+ const miSearchElement = document.querySelector('mi-search');

miMapElement.addEventListener('mapsIndoorsReady', () => {
    miMapElement.getMapInstance().then((mapInstance) => {
        mapInstance.setCenter([-77.0362723, 38.8974905]); // The White House
    });
});

miSearchElement.addEventListener('results', (event) => {
    console.log(event.detail);
});
```

For more information on available events and how to configure the `<mi-search>` component, see [components.mapsindoors.com/search](https://components.mapsindoors.com/search/).

### Display a List of Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#show-a-list-of-search-results) <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

To display a list of search results you can append each search result to a list element.



* Insert the `<mi-list>` custom element below the search field in `<body>`.
* Add the `scroll-buttons-enabled` and `scroll-length` attributes.

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
  <mi-map-mapbox style="width: 600px; height: 600px;" access-token="YOUR_MAPBOX_ACCESS_TOKEN" mi-api-key="YOUR_MAPSINDOORS_API_KEY">
  </mi-map-mapbox>
  <mi-search style="width: 600px;" mapsindoors="true" placeholder="Search">
  </mi-search>
  <mi-list style="width: 600px; height: 400px;" scroll-buttons-enabled="true" scroll-length="200">
  </mi-list>
  <script src="main.js"></script>
</body>
</html>
```

For more information on how to configure the `<mi-list>` component, see [components.mapsindoors.com/list](https://components.mapsindoors.com/list/).

* Get a reference to the list element.
* Reset the list on every complete search.

```javascript
// main.js

const miMapElement = document.querySelector("mi-map-mapbox");
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
})

miSearchElement.addEventListener('results', (event) => {
  // Reset search results list
  miListElement.innerHTML = null;
});
```

* Add a _for_ loop and append every result to the search results list element.

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-mapbox');
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
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
});
```

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/web/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

To filter the map to only display the search results you can use the `filter` method.



```javascript
// main.js

const miMapElement = document.querySelector("mi-map-mapbox");
const miSearchElement = document.querySelector('mi-search');
const miListElement = document.querySelector('mi-list');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
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

To remove the location filter again, call `mapsIndoorsInstance.filter(null)`.

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/bd4n1qtr/" %}
{% endtab %}
{% endtabs %}
