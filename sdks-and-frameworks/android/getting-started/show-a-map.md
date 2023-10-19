---
description: >-
  Your environment is now fully configured, and you have the necessary Google
  Maps and MapsIndoors API keys. Next you will learn how to load a Google Maps
  map with MapsIndoors.
---

# Show a Map

### Show a Map with MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/map#show-a-map-with-mapsindoors) <a href="#show-a-map-with-mapsindoors" id="show-a-map-with-mapsindoors"></a>

{% hint style="info" %}
ASYNC

Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.
{% endhint %}

## Initialize MapsIndoors[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapsindoors) <a href="#initialize-mapsindoors" id="initialize-mapsindoors"></a>

We start by initializing. `MapsIndoors`. `MapsIndoors` is used to get and store all references to MapsIndoors-specific data. This includes access to all MapsIndoors-specific geodata.

Place the following initialization code in the `onCreate` method in the `MapsActivity` that displays the Google map. You should also assign the `mapFragment` view to a local variable, as we will use this later to initialize [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) inside the `onCreate`, after it has been created:

{% tabs %}
{% tab title="Java - Google Maps" %}
[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L63-L66)

```java
protected void onCreate(Bundle savedInstanceState) {
    ...
    mMapView = mapFragment.getView();
    MapsIndoors.load(getApplicationContext(), "YOUR_MAPSINDOORS_API_KEY", null);
    ...
}
```

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our Google map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the Google map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between Google Maps and MapsIndoors.

Here we use Google Maps logic to apply geodata onto the map. This also means we append logic onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to our Google map:

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L145-L167)

```java
void initMapControl(View view) {
    MPMapConfig mapConfig = new MPMapConfig.Builder(this, mMap, getString(R.string.google_maps_key), view, true).build();
    MapControl.create(mapConfig, (mapControl, miError) -> {
        mMapControl = mapControl;
        //Enable Live Data on the map
        enableLiveData();
        if (miError == null) {
            //No errors so getting the first venue (in the white house solution the only one)
            MPVenue venue = MapsIndoors.getVenues().getCurrentVenue();
            runOnUiThread( ()-> {
                if (venue != null) {
                    //Animates the camera to fit the new venue
                        mMap.animateCamera(CameraUpdateFactory.newLatLngBounds(LatLngBoundsConverter.toLatLngBounds(venue.getBounds()), 19));
                }
            });
        }
    });
}
```

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations:

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L137-L143)

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mMap = googleMap;

    if (mMapView != null) {
        initMapControl(mMapView);
    }
}
```
{% endtab %}

{% tab title="Kotlin - Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L52-L56)

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

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our Google map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the Google map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between Google Maps and MapsIndoors.

Here we use Google Maps logic to apply geodata onto the map. This also means we append logic onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to our Google map:

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L117-L135)

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

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations:

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google\_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L109-L115)

```kotlin
override fun onMapReady(googleMap: GoogleMap) {
    mMap = googleMap

    mapView?.let { view ->
        initMapControl(view)
    }
}
```
{% endtab %}

{% tab title="Java - Mapbox" %}
[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L66)

```java
protected void onCreate(Bundle savedInstanceState) {
    ...
    MapsIndoors.load(getApplicationContext(), "YOUR_MAPSINDOORS_API_KEY", null);
    ...
}
```

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our Google map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the Google map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between Google Maps and MapsIndoors.

Here we use Google Maps logic to apply geodata onto the map. This also means we append logic onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to our Google map:

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L138-L159)

```java
void initMapControl() {
    MPMapConfig mapConfig = new MPMapConfig.Builder(this, mMapboxMap, mMapView, getString(R.string.mapbox_access_token),true).build();
    //Creates a new instance of MapControl
    MapControl.create(mapConfig, (mapControl, miError) -> {
        mMapControl = mapControl;
        //Enable Live Data on the map
        enableLiveData();
        if (miError == null) {
            //No errors so getting the first venue (in the white house solution the only one)
            MPVenue venue = MapsIndoors.getVenues().getCurrentVenue();
            runOnUiThread( ()-> {
                if (venue != null) {
                    //Animates the camera to fit the new venue
                    CameraAnimationsUtils.flyTo(mMapboxMap, mMapboxMap.cameraForCoordinateBounds(CoordinateBoundsConverter.toCoordinateBounds(venue.getBounds()), new EdgeInsets(0,0,0,0), null, null));
                }
            });
        }
    });
}
```

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations:



[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java#L119)

```java
protected void onCreate(Bundle savedInstanceState) {
    ...
    initMapControl();
    ...
}
```
{% endtab %}

{% tab title="Kotlin - Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L48)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)
    ...
}
```

If you are not a customer you can use this demo MapsIndoors API key `d876ff0e60bb430b8fabb145`.

#### Initialize MapsControl[​](https://docs.mapsindoors.com/getting-started/android/v4/map#initialize-mapscontrol) <a href="#initialize-mapscontrol" id="initialize-mapscontrol"></a>

We now want to add all the data we get by initializing `MapsIndoors` to our Google map. This is done by initializing [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) onto the Google map. [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) is used as a layer between Google Maps and MapsIndoors.

Here we use Google Maps logic to apply geodata onto the map. This also means we append logic onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend to check our reference docs, and see if you can add a specific `Listener` through the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and always use those when possible.

Start by creating an `initMapControl` method which is used to initiate the [`MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) and assign it to our Google map:

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L101-L119)

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

In your `onMapReady` callback function, assign the `mMap` variable with the `GoogleMap` you get from the callback and call the `initMapControl` method with the `mMapView` you assigned in the `onCreate` to set up a Google map with MapsIndoors Venues, Buildings and Locations:



[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L98)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    initMapControl();
    ...
}
```
{% endtab %}
{% endtabs %}

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/android\_map\_gif.gif)

See the full example of MapsActivity here: [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google\_Maps/mapsindoorsgettingstartedjava) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google\_Maps/mapsindoorsgettingstartedkotlin)

The Mapbox examples can be found here: [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedjava/src/main/java/com/mapspeople/mapsindoorsgettingstartedjava/MapsActivity.java) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt)
