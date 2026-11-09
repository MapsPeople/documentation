# Switching Solutions

Some larger organisations may have not just multiple Venues, but also multiple Solutions in the MapsIndoors system. Therefore, it is naturally important to be able to switch between them.

At it's core, this is done simply by switching out the API key and reloading the system. However, there are a few more steps that can be done to ensure smooth transition between Solutions.

## Starting a Solution​ <a href="#starting-a-solution" id="starting-a-solution"></a>

To initialize MapsIndoors, do the following:

### Google Maps

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)

    mapFragment.view?.let {
        mapView = it
    }
    ...
}
override fun onMapReady(googleMap: GoogleMap) {
    mMap = googleMap

    mapView?.let { view ->
        initMapControl(view)
    }
}
fun initMapControl(view: View) {
    val config = MPMapConfig.Builder(this, mMap, getString(R.string.google_maps_key), view, true).build()
    //Creates a new instance of MapControl
    MapControl.create(config) { mapControl, miError ->
        if (miError == null) {
            mMapControl = mapControl!!
             //Orient your map to where you need data to be shown. This could be done by getting the default venue through MapsIndoors and panning the camera there
        }
    }
}
```

### Mapbox

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)
    ...
}
fun initMapControl(view: View) {
    val config = MPMapConfig.Builder(this, mMap, mapView, getString(R.string.mapbox_access_token),true).build()
    //Creates a new instance of MapControl
    MapControl.create(config) { mapControl, miError ->
        if (miError == null) {
            //Orient your map to where you need data to be shown. This could be done by getting the default venue through MapsIndoors and panning the camera there
        }
    }
}
```

## Switching Solutions​ <a href="#switching-solutions" id="switching-solutions"></a>

You switch Solutions by changing the active API key using `setAPIKey()`.

We recommend creating your own function to call in the future for this purpose, like the example here with `switchSolution()`:

### Google maps

```kotlin
private fun switchSolution() {
    mMapControl.onDestroy()
    MapsIndoors.load(applicationContext, "YOUR_SECONDARY_API_KEY", null)
    mMapView.getMapAsync(this)
}
```

### Mapbox

```kotlin
mMapControl.onDestroy()
MapsIndoors.load(applicationContext, "YOUR_SECONDARY_API_KEY", null)
initMapControl(mMapBoxMap, mMapView)
```
