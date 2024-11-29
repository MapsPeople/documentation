# Map Engine Setup

MapsIndoors provides support for both Mapbox GL JS v2 and v3. Our commitment to staying at the forefront of mapping technologies ensure that you have the flexibility to choose the Mapbox version that best align with your requirements.



## Mapbox V3

In order to access Mapbox v3 and its options, use `mapView`:&#x20;

```javascript
const mapView = new mapsindoors.mapView.MapboxV3View({
    accessToken: 'YOUR_MAPBOX_ACCESS_TOKEN',
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 },
    zoom: 17,
    maxZoom: 25,
    mapsIndoorsTransitionLevel: 17,
    showMapMarkers: false,
    lightPreset: 'dusk'
});

// Then the MapsIndoors SDK is initialized
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

<figure><img src="../../../../.gitbook/assets/Screenshot 2023-11-14 at 13.15.21.png" alt=""><figcaption><p>Mapbox v3 Map layout</p></figcaption></figure>

Mapbox' extruded buildings are not visible when MapsIndoors data is shown at the specified zoom level:

<figure><img src="../../../../.gitbook/assets/Screenshot 2023-11-14 at 13.16.34.png" alt=""><figcaption><p>MapsIndoors solution</p></figcaption></figure>

You can now choose between 4 different types of light: **day**, **dawn**, **night** or **dusk**.

<figure><img src="../../../../.gitbook/assets/Screenshot 2023-11-14 at 13.22.52.png" alt=""><figcaption><p>Mapbox v3 'dusk' light</p></figcaption></figure>

You can read more about the latest Mapbox v3 Standard Style [here](https://www.mapbox.com/blog/standard-core-style).



## Mapbox V2

In order to access Mapbox v2 and its options, use `mapView` and instantiate it as a new MapsIndoors object:

```javascript
const mapView = new mapsindoors.mapView.MapboxView({
    accessToken: YOUR_MAPBOX_ACCESS_TOKEN
    element: document.getElementById('map'),
    center: { lat: 38.8974905, lng: -77.0362723 },
    zoom: 17,
    maxZoom: 25,
});

// Then the MapsIndoors SDK is initialized
const mi = new mapsindoors.MapsIndoors({
    mapView: mapView,
    floor: "1",
    labelOptions: {
        pixelOffset: { width: 0, height: 18 }
    }
});
```

After successful `mapView` load you should be able to use Mapbox v2:

<figure><img src="../../../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Solution using Mapbox v2</p></figcaption></figure>



[\
](https://docs.mapsindoors.com/getting-started/web/)
