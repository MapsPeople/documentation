# Switching Solutions

Some larger organisations may have not just multiple Venues, but also multiple Solutions in the MapsIndoors system. Therefore, it is naturally important to be able to switch between them.

At it's core, this is done simply by switching out the API key and reloading the system. However, there are a few more steps that can be done to ensure smooth transition between Solutions.

## Starting a Solution​ <a href="#starting-a-solution" id="starting-a-solution"></a>

To initialize MapsIndoors, do the following:

### Google Maps

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_maps)

    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)

    val mapFragment = supportFragmentManager.findFragmentById(R.id.map) as SupportMapFragment
    mapView = mapFragment.view
    mapFragment.getMapAsync(this)
}

override fun onMapReady(googleMap: GoogleMap) {
    this.googleMap = googleMap

    mapView?.let { view ->
        initMapControl(view)
    }
}

fun initMapControl(view: View) {
    val config = MPMapConfig.Builder(this, googleMap, getString(R.string.google_maps_key), view, true).build()
    // Creates a new instance of MapControl
    MapControl.create(config) { mapControl, error ->
        if (mapControl == null) {
            Log.e("MapsActivity", "Could not create MapControl: ${error?.message}")
            return@create
        }
        this.mapControl = mapControl
        // Orient your map to where you need data to be shown. This could be done by getting the default venue through MapsIndoors and panning the camera there
    }
}
```

### Mapbox

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_maps)

    MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY", null)

    mapView = findViewById(R.id.mapView)
    mapboxMap = mapView.mapboxMap
    initMapControl()
}

fun initMapControl() {
    val config = MPMapConfig.Builder(this, mapboxMap, mapView, getString(R.string.mapbox_access_token), true).build()
    // Creates a new instance of MapControl
    MapControl.create(config) { mapControl, error ->
        if (mapControl == null) {
            Log.e("MapsActivity", "Could not create MapControl: ${error?.message}")
            return@create
        }
        this.mapControl = mapControl
        // Orient your map to where you need data to be shown. This could be done by getting the default venue through MapsIndoors and panning the camera there
    }
}
```

## Switching Solutions​ <a href="#switching-solutions" id="switching-solutions"></a>

You switch Solutions by destroying the current `MapControl` and loading MapsIndoors again with the other Solution's API key through `MapsIndoors.load()`.

We recommend creating your own function to call in the future for this purpose, like the example here with `switchSolution()`:

### Google maps

```kotlin
private fun switchSolution() {
    mapControl.onDestroy()
    MapsIndoors.load(applicationContext, "YOUR_SECONDARY_API_KEY", null)
    // Recreate MapControl from onMapReady, once the map is ready again
    (supportFragmentManager.findFragmentById(R.id.map) as SupportMapFragment).getMapAsync(this)
}
```

### Mapbox

```kotlin
private fun switchSolution() {
    mapControl.onDestroy()
    MapsIndoors.load(applicationContext, "YOUR_SECONDARY_API_KEY", null)
    initMapControl()
}
```
