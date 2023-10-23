# Display a Map

_MJE: I propose existing content below is deleted as that's already covered in the getting-started. This page should have all the details instead_

**Google**:

how to use JSON styling.

how to use Cloud Styling.

how to enable vector map and tilt / rotation with a warning (2D models etc.).

how to control font (if possible) and labels.

**Mapbox**:

how to set the map style.

how to control label font and style.





***



### Overview[​](https://docs.mapsindoors.com/simple-map-web#overview) <a href="#overview" id="overview"></a>

In this guide you will learn to load a base map - Google Maps or Mapbox - with a MapsIndoors map on top. The full code example is shown in the JSFiddle below <mark style="background-color:red;">(Fiddle is missing)</mark>, but will be run through bit by bit in this guide.



#### Loading the MapsIndoors SDK[​](https://docs.mapsindoors.com/simple-map-web#loading-the-mapsindoors-sdk) <a href="#loading-the-mapsindoors-sdk" id="loading-the-mapsindoors-sdk"></a>

The MapsIndoors SDK is loaded by using a script tag like the one below:

```javascript
<script src="https://app.mapsindoors.com/mapsindoors/js/sdk/DevelopmentReleases/4.0.0-rc.1/mapsindoors-4.0.0-rc.1.js?apikey=YOUR_MAPSINDOORS_API_KEY"></script>
```

The `apikey` parameter contains your application's MapsIndoors API key.

#### Loading the base map[​](https://docs.mapsindoors.com/simple-map-web#loading-the-google-maps-javascript-api) <a href="#loading-the-google-maps-javascript-api" id="loading-the-google-maps-javascript-api"></a>

A base map is loaded by using a script tag like the one below:

{% tabs %}
{% tab title="Mapbox" %}
```html
<script src='https://api.mapbox.com/mapbox-gl-js/v1.10.1/mapbox-gl.js'></script>
```
{% endtab %}

{% tab title="Google Maps" %}
```html
<script src="https://maps.googleapis.com/maps/api/js?libraries=geometry&key=YOUR_GOOGLE_API_KEY"></script>
```

The `libraries` parameter is for loading additional libraries for the Google Maps API. The MapsIndoors SDK is dependent on the Geometry library from Google.

The `key` parameter contains your Google Maps API key. Look [here](https://developers-dot-devsite-v2-prod.appspot.com/maps/documentation/javascript/get-api-key) for more information about how to obtain a key.
{% endtab %}
{% endtabs %}

#### Setting Up the MapView[​](https://docs.mapsindoors.com/simple-map-web#setting-up-the-mapview) <a href="#setting-up-the-mapview" id="setting-up-the-mapview"></a>

{% tabs %}
{% tab title="Mapbox" %}
```javascript
const mapViewOptions = {
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN',
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 }, // The White House
    zoom: 17,
};
const mapViewInstance = new mapsindoors.mapView.MapboxView(mapViewOptions);
const mapsIndoorsInstance = new mapsindoors.MapsIndoors({
    mapView: mapViewInstance,
});
```

* `element` is the DOM element on the page that will contain the map. `document.getElementById('map')`
* `center` is the geographical point on which the map is centered.
* `zoom` is the initial zoom level the map will be displayed at.

For a full list of parameters, see the [mapView.MapboxView](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.MapboxView.html) reference.
{% endtab %}

{% tab title="Google Maps" %}
```javascript
const mapView = new mapsindoors.mapView.GoogleMapsView({
    element: document.getElementById("map"),
    center: {
        lat: 38.8976067,
        lng: -77.0365872,
    },
    zoom: 19,
    maxZoom: 21,
});
```

* `element` is the DOM element on the page that will contain the map. `document.getElementById('map')`
* `center` is the geographical point on which the map is centered.
* `zoom` is the initial zoom level the map will be displayed at.
* The `maxZoom` parameter is set to disable the map from zooming further in than level 21 which is the current maximum.&#x20;

For a full list of parameters, see the [mapView.GoogleMapsView](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.mapView.GoogleMapsView.html) reference.
{% endtab %}
{% endtabs %}

#### Initializing MapsIndoors[​](https://docs.mapsindoors.com/simple-map-web#initializing-mapsindoors) <a href="#initializing-mapsindoors" id="initializing-mapsindoors"></a>

```javascript
const mapsIndoors = new mapsindoors.MapsIndoors({
    mapView: mapView,
});
```

A new instance of the MapsIndoors class is created and assigns the MapboxView or GoogleMapsView to the `mapView` parameter.

#### Adding a Floor Selector[​](https://docs.mapsindoors.com/simple-map-web#adding-a-floor-selector) <a href="#adding-a-floor-selector" id="adding-a-floor-selector"></a>

```javascript
const googleMap = mapView.getMap();
const floorSelector = document.createElement("div");
new mapsindoors.FloorSelector(floorSelector, mapsIndoors);
googleMap.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelector);
```

A reference to the Google Map is obtained by calling `getMap` on the `mapView`.

The `floorSelector` is created by calling the `new mapsindoors.FloorSelector(floor selector, mapsIndoors);` passing in a DOM element and an instance of MapsIndoors.

`googleMap.controls[google.maps.ControlPosition.RIGHT_TOP].push(floorSelector);` adds the Floor Selector to upper right corner of the map as a map control.



ASYNC

Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.



LATEST VERSION

This "Getting Started" guide is created using a specific version of the SDK. When moving beyond the "Getting Started" guide, please be sure to use the latest version of the SDK.
