---
description: >-
  Your environment is now fully configured, and you have the necessary Google
  Maps and MapsIndoors API keys. Next you will learn how to load a map with
  MapsIndoors.
---

# Show a Map

### Show a Map with MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/map#show-a-map-with-mapsindoors) <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

{% hint style="info" %}
ASYNC

Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.
{% endhint %}

## Initialize MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapsindoors) <a href="#initialize-mapsindoors" id="initialize-mapsindoors"></a>

We start by initializing `MapsIndoors`. `MapsIndoors` is used to get and store all references to MapsIndoors-specific data. This includes access to all MapsIndoors-specific geodata.

Place the following initialization code in the `onCreate` method in the `MapsActivity` that displays the Google map. You should also assign the `mapFragment` view to a local variable, as we will use this later to initialize [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) inside the `onCreate`, after it has been created:

{% tabs %}
{% tab title="Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L44-L107)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)

    mapFragment.view?.let {
        mapView = it
    }
    ...
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L99)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)
    ...
}
```
{% endtab %}
{% endtabs %}

If you do not have your own key, you can use this demo MapsIndoors API key: `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between the map provider and MapsIndoors.

{% hint style="info" %}
[`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) uses Google Maps listeneres to control some map logic. So be aware that  using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.
{% endhint %}

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to mMap:

{% tabs %}
{% tab title="Google maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L117-L135)

```kotlin
private fun initMapControl(view: View) {
    MPMapConfig mapConfig = new MPMapConfig.Builder(this, mMap, getString(R.string.google_maps_key), view, true).build();
    //Creates a new instance of MapControl
    MapControl.create(config) { mapControl, miError ->
        if (miError == null) {
            mMapControl = mapControl!!
            //Enable live data on the map
            enableLiveData()
            //No errors so getting the first venue (in the white house solution the only one)
            val venue = MapsIndoors.getVenues()?.currentVenue
            venue?.bounds?.let {
                runOnUiThread {
                    //Animates the camera to fit the new venue
                    mMap.animateCamera(CameraUpdateFactory.newLatLngBounds(LatLngBoundsConverter.toLatLngBounds(it), 19))
                }
            }
        }
    }
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L101-L119)

```kotlin
private fun initMapControl() {
    //Creates a new instance of MapControl
    val config = MPMapConfig.Builder(this, mMap, mapView, getString(R.string.mapbox_access_token),true).build()
    MapControl.create(config) { mapControl, miError ->
        if (miError == null) {
            mMapControl = mapControl!!
            //Enable live data on the map
            enableLiveData()
            //No errors so getting the first venue (in the white house solution the only one)
            val venue = MapsIndoors.getVenues()?.currentVenue
            venue?.bounds?.let {
                runOnUiThread {
                    //Animates the camera to fit the new venue
                    mMap.flyTo(mMap.cameraForCoordinateBounds(CoordinateBoundsConverter.toCoordinateBounds(it)))
                }
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations. For Mapbox you can simple call initMapControl inside your `onCreate`:\


{% tabs %}
{% tab title="Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L109-L115)

```kotlin
override fun onMapReady(googleMap: GoogleMap) {
    mMap = googleMap

    mapView?.let { view ->
        initMapControl(view)
    }
}
```
{% endtab %}

{% tab title="Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/79a8b7c22751048c7c064a63b067eb740cf5e50f/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L98)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    initMapControl();
    ...
}
```
{% endtab %}
{% endtabs %}

\
Expected result:

<figure><img src="../../../.gitbook/assets/android_map_gif.gif" alt=""><figcaption></figcaption></figure>

See the full example of MapsActivity here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google\_Maps/mapsindoorsgettingstartedkotlin)

The Mapbox examples can be found here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt)
