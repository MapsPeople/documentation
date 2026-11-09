---
description: >-
  Your environment is now fully configured, and you have the necessary Google
  Maps and MapsIndoors API keys. Next you will learn how to load a map with
  MapsIndoors.
---

# Show a Map

### Show a Map with MapsIndoors​ <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

{% hint style="info" %}
ASYNC

Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.
{% endhint %}

## Initialize MapsIndoors​ <a href="#initialize-mapsindoors" id="initialize-mapsindoors"></a>

We start by initializing `MapsIndoors`. `MapsIndoors` is used to get and store all references to MapsIndoors-specific data. This includes access to all MapsIndoors-specific geodata.

Place the following initialization code in the `onCreate` method in the `MapsActivity` that displays the Google map. You should also assign the `mapFragment` view to a local variable, as we will use this later to initialize [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) inside the `onCreate`, after it has been created:

{% tabs %}
{% tab title="Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L105)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_maps)

    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)

    val mapFragment = supportFragmentManager.findFragmentById(R.id.map) as SupportMapFragment
    mapView = mapFragment.view
    mapFragment.getMapAsync(this)
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L39-L96)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_maps)

    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)
    // ...
}
```
{% endtab %}
{% endtabs %}

If you do not have your own key, you can use this demo MapsIndoors API key: `02c329e6777d431a88480a09`.

#### Initialize MapsControl <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between the map provider and MapsIndoors.

{% hint style="info" %}
[`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) uses Google Maps listeneres to control some map logic. So be aware that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.
{% endhint %}

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to `mapControl`:

{% tabs %}
{% tab title="Google maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L115-L131)

```kotlin
private fun initMapControl(view: View) {
    val config = MPMapConfig.Builder(this, googleMap, getString(R.string.google_maps_key), view, true).build()
    // Creates a new instance of MapControl
    MapControl.create(config) { mapControl, error ->
        if (mapControl == null) {
            Log.e("MapsActivity", "Could not create MapControl: ${error?.message}")
            return@create
        }
        this.mapControl = mapControl
        // Enable live data on the map
        enableLiveData()
        // No errors, so get the default venue (in the White House solution, the only one)
        val bounds = MapsIndoors.venues?.defaultVenue?.bounds ?: return@create
        // The callback can arrive on a background thread, so move the camera from the main thread
        lifecycleScope.launch {
            // Animates the camera to fit the venue
            googleMap.animateCamera(CameraUpdateFactory.newLatLngBounds(LatLngBoundsConverter.toLatLngBounds(bounds), 19))
        }
    }
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L98-L114)

```kotlin
private fun initMapControl() {
    val config = MPMapConfig.Builder(this, mapboxMap, mapView, getString(R.string.mapbox_access_token), true).build()
    // Creates a new instance of MapControl
    MapControl.create(config) { mapControl, error ->
        if (mapControl == null) {
            Log.e("MapsActivity", "Could not create MapControl: ${error?.message}")
            return@create
        }
        this.mapControl = mapControl
        // Enable live data on the map
        enableLiveData()
        // No errors, so get the default venue (in the White House solution, the only one)
        val bounds = MapsIndoors.venues?.defaultVenue?.bounds ?: return@create
        // The callback can arrive on a background thread, so move the camera from the main thread
        lifecycleScope.launch {
            // Animates the camera to fit the venue
            mapboxMap.flyTo(mapboxMap.cameraForCoordinateBounds(CoordinateBoundsConverter.toCoordinateBounds(bounds)))
        }
    }
}
```
{% endtab %}
{% endtabs %}

In your `onMapReady` callback function, assign the `googleMap` property with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations. For Mapbox, get the `MapboxMap` from your `MapView` and call `initMapControl` inside your `onCreate`:

{% tabs %}
{% tab title="Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L107-L113)

```kotlin
override fun onMapReady(googleMap: GoogleMap) {
    this.googleMap = googleMap

    mapView?.let { view ->
        initMapControl(view)
    }
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L95)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    mapView = findViewById(R.id.mapView)
    mapboxMap = mapView.mapboxMap
    initMapControl()
}
```
{% endtab %}
{% endtabs %}

\
Expected result:

<figure><img src="../../../.gitbook/assets/android_map_gif.gif" alt=""><figcaption></figcaption></figure>

See the full example of MapsActivity here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google_Maps/mapsindoorsgettingstartedkotlin)

The Mapbox examples can be found here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt)
