# Tutorial

Throughout the Getting Started guide, you will modify and extend the same code to create your map. Let's start by creating the initial app.

## Set Up Your Environment[​](https://docs.mapsindoors.com/getting-started/web/new-project#set-up-your-environment) <a href="#set-up-your-environment" id="set-up-your-environment"></a>

1. If you do not have prior development experience, you can install an Integrated Development Environment (IDE), e.g. [Visual Studio Code](https://code.visualstudio.com/).
2. Start by creating a new project folder. The location is not important, just remember the location, and ensure your newly created project folder is empty.
3.  Inside that, create two empty files: `index.html` and `main.js`.

    > The file `index.html` is the entry point for our application and contains the HTML code. The file `main.js` will be read by `index.html` and consists of the JavaScript code for the actual application to run. To try the app you will be creating, run `index.html` in your web browser.
4. Open `index.html`. Create a basic HTML structure and include the `main.js` file as follows:

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MapsIndoors</title>
</head>
<body>
  <script src="main.js"></script>
</body>
</html>
```

Both here, and in the following examples, you will always be able to see which of the two files the code should go in, by looking at the first line, where the name of the file is written.



Your environment is now fully configured, and you have the necessary API keys. Next, you will learn how to display a map with MapsIndoors.

## Display a Map with MapsIndoors[​](https://docs.mapsindoors.com/getting-started/web/map#show-a-map-with-mapsindoors) <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

{% tabs %}
{% tab title="Google Maps - Manually" %}
The MapsIndoors SDK is hosted on a Content Delivery Network (CDN) and should be loaded using a script tag.

Insert the MapsIndoors SDK script tag into `<head>`, followed by the Google Maps script tag:

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
  <script src="main.js"></script>
</body>
</html>
```

Remember to add your API keys to the links in your code. If you do not have your own API key, you can use the demo MapsIndoors API key: `d876ff0e60bb430b8fabb145`.

Add an empty `<div>` element to `<body>` with the `id` attribute set to `"map"`:

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
</body>
</html>
```

To load data and display it on the map, we need to create a new _instance_ of the [`MapsIndoors` class](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#MapsIndoors) with a [`mapView` instance](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html#GoogleMapsView) with a few _properties_ set. This is all done by placing the following code in the `main.js` file you created earlier:

```javascript
// main.js

const mapViewOptions = {
  element: document.getElementById('map'),
  center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
  zoom: 17,
  maxZoom: 22,
};
const mapViewInstance = new mapsindoors.mapView.GoogleMapsView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({
    mapView: mapViewInstance
});
```

What happens in this snippet is we create a `mapViewInstance` that pulls up a [`GoogleMapsView`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html) with some [`mapViewOptions`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html). The options define which element in the html-file to display the map in (in this case `<div id="map">`), where the map should center, what zoom level to display, and what the max zoom level is.

You should now see a map from your chosen map engine with MapsIndoors data loaded on top.

### Display a Floor Selector <a href="#show-a-floor-selector" id="show-a-floor-selector"></a>

Next, we'll add a Floor Selector for changing between floors.

First, we add an empty `<div>` element programmatically. Then we create a new [`FloorSelector` _instance_](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/FloorSelector.html) and push the `floorSelectorElement` to the `googleMapsInstance` to position it as a map controller:

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
```

You should now be able to switch between floors.

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/wgvdjpsu/2/" %}
{% endtab %}

{% tab title="Google Maps - MI Components" %}
Using the `<mi-map-googlemaps>` component, the MapsIndoors JS SDK is automatically inserted into the DOM when initialized.

The MapsIndoors Web Components library can be loaded using [unpkg](https://unpkg.com/), a widely used CDN for everything on [npm](https://npmjs.com/).

Insert script tag into `<head>`:

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
  <script src="main.js"></script>
</body>
</html>
```

Check [@mapsindoors/components](https://www.npmjs.com/package/@mapsindoors/components) for latest version.

After you added the script tag into `<head>`, add the `<mi-map-googlemaps>` custom element into `<body>`. We need to add and populate the `gm-api-key` and `mi-api-key` attributes with your API keys as well:

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
  style="width: 600px;
  height: 600px;"
  gm-api-key="YOUR_GOOGLE_MAPS_API_KEY"
  mi-api-key="YOUR_MAPSINDOORS_API_KEY">
</mi-map-googlemaps>
  <script src="main.js"></script>
</body>
</html>
```

Remember to add your API keys where indicated. You can use the demo MapsIndoors API key: `d876ff0e60bb430b8fabb145`

To center the map correctly, you need need the Google Maps _instance_ in your JavaScript-file.

First, we get a reference to the `<mi-map-googlemaps>` element. Then we attach the [`mapsIndoorsReady`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#event:ready) event listener so we'll know when MapsIndoors is ready after loading. Lastly, on the `mapsIndoorsReady` event, get the Google Maps _instance_ and call its [`setCenter` method](https://developers.google.com/maps/documentation/javascript/reference/map#Map.setCenter) to center the map on the loaded data:

```javascript
// main.js

const miMapElement = document.querySelector('mi-map-googlemaps');

miMapElement.addEventListener('mapsIndoorsReady', () => {
  miMapElement.getMapInstance().then((mapInstance) => {
    mapInstance.setCenter({ lat: 38.8974905, lng: -77.0362723 }); // The White House
  });
})
```

> For more information on how to configure the `<mi-map-googlemaps>` component, see [components.mapsindoors.com/map-googlemaps](https://components.mapsindoors.com/map-googlemaps/).

You should now see a map from your chosen map engine with MapsIndoors data loaded on top.

### Display a Floor Selector[​](https://docs.mapsindoors.com/getting-started/web/map#show-a-floor-selector) <a href="#show-a-floor-selector" id="show-a-floor-selector"></a>

Next, we'll add a Floor Selector for changing between floors.

Using the `<mi-map-googlemaps>` element, you can add the [floorSelectorControlPosition attribute](https://components.mapsindoors.com/map-googlemaps/) to your existing element. In this case with the value `"TOP_RIGHT"`:

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
  style="width: 600px;
  height: 600px;"
  gm-api-key="YOUR_GOOGLE_MAPS_API_KEY"
  mi-api-key="YOUR_MAPSINDOORS_API_KEY"
  floor-selector-control-position="TOP_RIGHT">
  </mi-map-googlemaps>
  <script src="main.js"></script>
</body>
</html>
```

You should now be able to switch between floors.&#x20;

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/h0gdbmo4/1/" %}
{% endtab %}

{% tab title="Mapbox - Manually" %}
The MapsIndoors SDK is hosted on a Content Delivery Network (CDN) and should be loaded using a script tag.

Insert the MapsIndoors SDK script tag into `<head>`, followed by the Mapbox `script` and `style` tag:

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
  <script src="main.js"></script>
</body>
</html>
```

> Remember to add your API keys to the links in your code. You can use the demo MapsIndoors API key: `d876ff0e60bb430b8fabb145`.

Add an empty `<div>` element to `<body>` with the `id` attribute set to "map":

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
</body>
</html>
```

To load data and display it on the map, we need to create a new _instance_ of the [`MapsIndoors` class](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#MapsIndoors) with a [`mapView` instance](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxView.html) with a few _properties_ set. This is all done by placing the following code in the `main.js` file you created earlier:

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
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({
    mapView: mapViewInstance,
});
```

What happens in this snippet is we create a `mapViewInstance` that pulls up a [`MapboxView`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxView.html) with some [`mapViewOptions`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxView.html). The options define which element in the html-file to display the map in (in this case `<div id="map">`), where the map should center, what zoom level to display, and what the max zoom level is.

You should now see a map from your chosen map engine with MapsIndoors data loaded on top.

### Display a Floor Selector <a href="#show-a-floor-selector" id="show-a-floor-selector"></a>

Next, we'll add a Floor Selector for changing between floors.

First, we add an empty `<div>` element programmatically. Then we create a new [`FloorSelector` _instance_](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/FloorSelector.html) and push the `floorSelectorElement` to the `mapboxInstance` to position it as a map controller:

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
const mapboxInstance = mapViewInstance.getMap();

// Floor Selector
const floorSelectorElement = document.createElement('div');
new mapsindoors.FloorSelector(floorSelectorElement, mapsIndoorsInstance);
mapboxInstance.addControl({ onAdd: function () { return floorSelectorElement }, onRemove: function () { } });
```

{% embed url="https://docs.mapbox.com/mapbox-gl-js/api/map/#map#addcontrol" %}

> See all available control positions in the [Mapbox Documentation](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#addcontrol).

You should now be able to switch between floors.&#x20;

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/o12hrcL8/2/" %}
{% endtab %}

{% tab title="Mapbox - MI Components" %}
Using the `<mi-map-mapbox>` component, the MapsIndoors JS SDK is automatically inserted into the DOM when initialized.

The MapsIndoors Web Components library can be loaded using [unpkg](https://unpkg.com/), a widely used CDN for everything on [npm](https://npmjs.com/).

Insert script tag into `<head>`:

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
  <script src="main.js"></script>
</body>
</html>
```

Check [@mapsindoors/components](https://www.npmjs.com/package/@mapsindoors/components) for latest version.

After you added the script tag into `<head>`, add the `<mi-map-mapbox>` custom element into `<body>`. We need to add and populate the `accessToken` and `mi-api-key` attributes with your access token and API key as well:

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
  <script src="main.js"></script>
</body>
</html>
```

Remember to add your API keys where indicated. You can use the demo MapsIndoors API key: `d876ff0e60bb430b8fabb145`.

To center the map correctly, you need need the Mapbox _instance_ in your JavaScript-file.

First we get a reference to the `<mi-map-mapbox>` element. Then we attach the [`mapsIndoorsReady`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.MapsIndoors.html#event:ready) event listener so we'll know when MapsIndoors is ready after loading. Lastly, on the `mapsIndoorsReady` event, get the Mapbox _instance_ and call its [`setCenter` method](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#setcenter) to center the map on the loaded data:

```javascript
// main.js
const miMapElement = document.querySelector('mi-map-mapbox');
miMapElement.addEventListener('mapsIndoorsReady', () => {
    miMapElement.getMapInstance().then((mapInstance) => {
        mapInstance.setCenter([-77.0362723, 38.8974905]); // The White House
    });
});
```

For more information on how to configure the `<mi-map-mapbox>` component, see [components.mapsindoors.com/map-mapbox](https://components.mapsindoors.com/map-mapbox/).

You should now see a map from your chosen map engine with MapsIndoors data loaded on top.

### Display a Floor Selector <a href="#show-a-floor-selector" id="show-a-floor-selector"></a>

Next, we'll add a Floor Selector for changing between floors.

Using the `<mi-map-mapbox>` element, you can add the [floorSelectorControlPosition attribute](https://components.mapsindoors.com/map-mapbox/) to your existing element. In this case with the value `"TOP_RIGHT"`:

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
<mi-map-mapbox style="width: 600px; height: 600px;" access-token="YOUR_MAPBOX_ACCESS_TOKEN" mi-api-key="YOUR_MAPSINDOORS_API_KEY" floor-selector-control-position="TOP_RIGHT">
  </mi-map-mapbox>
  <script src="main.js"></script>
</body>
</html>
```

> See all available control positions in the [Mapbox Documentation](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#addcontrol).

You should now be able to switch between floors.&#x20;

Here's a JSFiddle demonstrating the result you should have by now:

{% embed url="https://jsfiddle.net/mapspeople/vr1wkmho/1/" %}
{% endtab %}
{% endtabs %}

## Create a Search Experience

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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
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
  <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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
  <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
  <link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css">
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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
  <link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css">
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
  <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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
  <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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



## Getting Directions

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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
<script src="https://maps.googleapis.com/maps/api/js?libraries=geometry&key=YOUR_GOOGLE_MAPS_API_KEY"></script>
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
    <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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
  <script src="https://app.mapsindoors.com/mapsindoors/js/sdk/4.24.8/mapsindoors-4.24.8.js.gz?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
  <script src="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
  <link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css">
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
  <script type="module" src="https://unpkg.com/@mapsindoors/components@13.7.1/dist/mi-components/mi-components.esm.js"></script>
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
