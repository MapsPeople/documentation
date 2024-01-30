# Map Engine Setup

MapsIndoors is built on top of an external map engine, either Mapbox or Google Maps.&#x20;

You'll need an Access Token for your chosen map engine with the relevant APIs enabled and a MapsIndoors API key to get started building your own app.

## Option 1: Get your Mapbox Access Token[​](https://docs.mapsindoors.com/getting-started/web/prerequisites#option-2-get-your-mapbox-access-token) <a href="#option-1-get-your-google-maps-api-keys" id="option-1-get-your-google-maps-api-keys"></a>

You need to create and set up a Mapbox Access Token by following the steps in the link below:

* [<mark style="color:purple;">Mapbox Access Token documentation</mark>](https://docs.mapbox.com/help/getting-started/access-tokens/)

Remember to enable relevant _scopes_ on the Mapbox Access Token, such as:

* [<mark style="color:purple;">Vector Tiles API</mark>](https://docs.mapbox.com/api/maps/vector-tiles/)
* [<mark style="color:purple;">Styles API</mark>](https://docs.mapbox.com/api/maps/styles/)
* [<mark style="color:purple;">Directions API</mark>](https://docs.mapbox.com/api/navigation/directions/)

MapsIndoors provides support for both Mapbox GL JS v2 and v3. Our commitment to staying at the forefront of mapping technologies ensure that you have the flexibility to choose the Mapbox version that best align with your requirements.

### Mapbox v3

In order to access Mapbox v3 and its options, use `mapView`:&#x20;

```
const mapView = new mapsindoors.mapView.MapboxV3View({
    accessToken: YOUR_MAPBOX_ACCESS_TOKEN
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 },
    zoom: 17,
    maxZoom: 25,
    mapsIndoorsTransitionLevel: 17,
    showMapMarkers: false,
    lightPreset: 'dusk'
});

//Then the MapsIndoors SDK is initialized
const mi = new mapsindoors.MapsIndoors({
    mapView: mapView,
    floor: "1",
    labelOptions: {
        pixelOffset: { width: 0, height: 18 }
    }
});
```

For Mapbox v3, we exposed three new constructor parameters for Mapbox v3:&#x20;

* `mapsIndoorsTransitionLevel: number`- controls transition between Mapbox and MapsIndoors data. Defaults to `17`. Setting it to `17` will make a transition between Mapbox and MapsIndoors data between zoom levels `17` to `18`.
* `showMapMarkers: boolean` - boolean parameter that dictates if Mapbox map markers such as POIs should be shown or not.  By not setting this parameter, Mapbox map markers will be hidden as soon as MapsIndoors data is shown.
* `lightPreset: string` - sets global light. Can be set to: **day**, **dawn**, **dusk** or **night**. Defaults to **day**.

The new Mapbox v3 Standard Style design provides the new design of the map and 3D buildings.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-11-14 at 13.15.21.png" alt=""><figcaption><p>Mapbox v3 Map layout</p></figcaption></figure>

Mapbox' extruded buildings are not visible when MapsIndoors data is shown at the specified zoom level:

<figure><img src="../../../.gitbook/assets/Screenshot 2023-11-14 at 13.16.34.png" alt=""><figcaption><p>MapsIndoors solution</p></figcaption></figure>

You can now choose between 4 different types of light: **day**, **dawn**, **night** or **dusk**.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-11-14 at 13.22.52.png" alt=""><figcaption><p>Mapbox v3 'dusk' light</p></figcaption></figure>

You can read more about the latest Mapbox v3 Standard Style [here](https://www.mapbox.com/blog/standard-core-style).

### Mapbox v2

In order to access Mapbox v2 and its options, use `mapView` and instantiate it as a new MapsIndoors object:

```
const mapView = new mapsindoors.mapView.MapboxView({
    accessToken: YOUR_MAPBOX_ACCESS_TOKEN
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 },
    zoom: 17,
    maxZoom: 25,
});

//Then the MapsIndoors SDK is initialized
const mi = new mapsindoors.MapsIndoors({
    mapView: mapView,
    floor: "1",
    labelOptions: {
        pixelOffset: { width: 0, height: 18 }
    }
});
```

After successful `mapView` load you should be able to use Mapbox v2:

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Solution using Mapbox v2</p></figcaption></figure>

## Option 2: Get your Google Maps API Keys[​](https://docs.mapsindoors.com/getting-started/web/prerequisites#option-1-get-your-google-maps-api-keys) <a href="#option-1-get-your-google-maps-api-keys" id="option-1-get-your-google-maps-api-keys"></a>

To make sure you can use the full feature set of MapsIndoors you'll need to enable these services.&#x20;

* [<mark style="color:purple;">Maps JavaScript API</mark>](https://console.cloud.google.com/apis/library/maps-backend.googleapis.com)
* [<mark style="color:purple;">Google Maps Distance Matrix API</mark>](https://console.cloud.google.com/apis/library/distance-matrix-backend.googleapis.com)
* [<mark style="color:purple;">Google Maps Directions API</mark>](https://console.cloud.google.com/apis/library/directions-backend.googleapis.com)
* [<mark style="color:purple;">Google Places API Web Service</mark>](https://console.cloud.google.com/apis/library/places-backend.googleapis.com)

Then you need to create your Google Maps API key by following the link below:

* [<mark style="color:purple;">Create Google Maps API key</mark>](https://developers.google.com/maps/documentation/javascript/get-api-key)&#x20;

If you apply restrictions to your key, remember to include the Google services listed above.

### Get your MapsIndoors API Key[​](https://docs.mapsindoors.com/getting-started/web/prerequisites#get-your-mapsindoors-api-key) <a href="#get-your-mapsindoors-api-key" id="get-your-mapsindoors-api-key"></a>

In order to include MapsIndoors in your app, you need a MapsIndoors API key. If you do not yet have access to your MapsIndoors API key, you can use the demo API key `d876ff0e60bb430b8fabb145` to follow the guide.

If you have access to the MapsIndoors CMS, you'll find your MapsIndoors API key as described [<mark style="color:purple;">here</mark>](../../../products/cms/interface-overview.md#api-keys).&#x20;

[\
](https://docs.mapsindoors.com/getting-started/web/)
