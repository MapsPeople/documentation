# Display a Map

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





