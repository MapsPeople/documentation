# Using multiple Map Providers

It is possible to use `MapsIndoors` with multiple map providers, from this point we will call them platforms, like `Mapbox` and `Google Maps`, in the same app. The basics of this approach is to create a generic interface for accessing the maps, as well as provide some utility in setting up `MapsIndoors`.

In this article we will: Install multiple platforms, create a `Map` interface, create a `Fragment` interface to hold the `MapView`s, create implementations of the `Map` interface for each platform, and then blend them together in a single app.

We will use `Google Maps` and `Mapbox` as examples.

## Build gradle

First we have to add both platforms to our build file, it is important that both versions of MapsIndoors use the same version, otherwise we might experience issue with the interface.

{% code title="build.gradle" overflow="wrap" lineNumbers="true" %}

```gradle
ext {
  mapsindoors = "5.0.0"
  google_maps = "19.0.0"
  mapbox = "11.18.1"
}
dependencies {
  implementation "com.mapspeople.mapsindoors:mapbox-v11:$mapsindoors"
  implementation "com.mapspeople.mapsindoors:googlemaps:$mapsindoors"
  implementation "com.mapbox.maps:android-ndk27:$mapbox"
  implementation "com.google.android.gms:play-services-maps:$google_maps"

  // MapsIndoors and Mapbox repos
  repositories {
    maven {
      url 'https://maven.mapsindoors.com/'
    }
    maven {
      url 'https://api.mapbox.com/downloads/v2/releases/maven'
      authentication {
        basic(BasicAuthentication)
      }
      credentials {
        // Do not change the username below.
        // This should always be `mapbox` (not your username).
        username = "mapbox"
        // Use the secret token you stored in gradle.properties as the password
        password = project.properties['MAPBOX_DOWNLOADS_TOKEN'] ?: ""
      }
    }
  }
}
```

{% endcode %}

## Map Interface

The map interface should be as lean as possible, only containing the methods you need to use on the maps, in this example we have added 4 methods for moving the camera, as well the ability to read the current position, projection as well as turning the compass on/off.

This interface can be extended or shrunk to your hearts content, but remember that each method added will have to be implemented on both platforms.

{% code title="MIMap.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds

interface MIMap {
    fun animateCamera(mpCameraPosition: MPCameraPosition?)
    fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int)
    fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int)
    fun moveCamera(mpCameraPosition: MPCameraPosition?)
    val cameraPosition: MPCameraPosition
    val projection: MPIProjection
    var isCompassEnabled: Boolean
}
```

{% endcode %}

## MapFragment Class

The `MapFragment` is a container for the platforms' `MapView`s, it should be able to tell when the `MapView` is ready for content and hand us a Map instance at that point, and create the start of a `MPMapConfig` which ensure the app can remain ignorant of the platform implementations.

First we create the interface we should follow, it should implement the `Fragment` class so that we can manage it like a fragment.

### Interface

{% code title="MapFragment.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.annotation.LayoutRes
import androidx.fragment.app.Fragment
import com.mapsindoors.core.MPIMapConfig

abstract class MapFragment(@LayoutRes contentLayoutId: Int) : Fragment(contentLayoutId) {
    // Suspends until the platform map is ready, and returns it
    abstract suspend fun awaitMap(): MIMap
    abstract fun getMapConfigBuilder(): MPIMapConfig.Builder
}
```

{% endcode %}

### Map Fragment

Then we create the platform implementations, but lets just leave actual implementations blank for now, and focus on creating the `Map` implementation.

{% tabs %}
{% tab title="Google Maps" %}

For Google Maps we also implement the `OnMapReadyCallback` to get a callback when the map is ready for use.

Go [here](#views) to see how the fragment layout is made.

{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIMapConfig

class GoogleMapFragment : MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {
    override suspend fun awaitMap(): MIMap {
        TODO("Not yet implemented")
    }

    override fun getMapConfigBuilder(): MPIMapConfig.Builder {
        TODO("Not yet implemented")
    }

    override fun onMapReady(googleMap: GoogleMap) {
        TODO("Not yet implemented")
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}

Go [here](#views) to see how the fragment layout is made.

{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.mapsindoors.core.MPIMapConfig

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {
    override suspend fun awaitMap(): MIMap {
        TODO("Not yet implemented")
    }

    override fun getMapConfigBuilder(): MPIMapConfig.Builder {
        TODO("Not yet implemented")
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Map implementation

We create the `Map` implementation as a nested class in the `MapFragment`, this helps encapsulate it, as well as keeping all the platform implementation in a single file.

{% tabs %}
{% tab title="Google Maps" %}
{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.googlemaps.MPProjection
import com.mapsindoors.googlemaps.converters.toCameraPosition
import com.mapsindoors.googlemaps.converters.toLatLngBounds
import com.mapsindoors.googlemaps.converters.toMPCameraPosition

// Nested inside GoogleMapFragment
class MIGoogleMap(private val map: GoogleMap) : MIMap {
    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
        // sometimes the cameraPosition we create is nullable, lets handle it here instead of outside the method
        mpCameraPosition ?: return
        map.animateCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()))
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
        mpCameraPosition ?: return
        map.animateCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()), duration, null)
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
        latLngBounds ?: return
        map.moveCamera(CameraUpdateFactory.newLatLngBounds(latLngBounds.toLatLngBounds(), padding))
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
        mpCameraPosition ?: return
        map.moveCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()))
    }

    override val cameraPosition: MPCameraPosition
        get() = map.cameraPosition.toMPCameraPosition()

    override val projection: MPIProjection
        get() = MPProjection(map.projection, map.cameraPosition.zoom, map.cameraPosition.bearing)

    override var isCompassEnabled: Boolean
        get() = map.uiSettings.isCompassEnabled
        set(value) {
            map.uiSettings.isCompassEnabled = value
        }
}
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}
{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.mapbox.maps.CameraOptions
import com.mapbox.maps.EdgeInsets
import com.mapbox.maps.MapView
import com.mapbox.maps.MapboxMap
import com.mapbox.maps.plugin.animation.MapAnimationOptions
import com.mapbox.maps.plugin.animation.flyTo
import com.mapbox.maps.plugin.compass.compass
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.mapbox.MPProjection
import com.mapsindoors.mapbox.converters.toCoordinateBounds
import com.mapsindoors.mapbox.converters.toMPLatLng
import com.mapsindoors.mapbox.converters.toPoint

// Nested inside MapboxMapFragment
class MIMapboxMap(private val map: MapboxMap, private val mapView: MapView) : MIMap {
    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
        mpCameraPosition ?: return
        map.flyTo(mpCameraPosition.toCameraOptions())
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
        mpCameraPosition ?: return
        map.flyTo(
            mpCameraPosition.toCameraOptions(),
            MapAnimationOptions.Builder().duration(duration.toLong()).build(),
        )
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
        latLngBounds ?: return
        val pad = padding.toDouble()
        map.setCamera(
            map.cameraForCoordinateBounds(
                latLngBounds.toCoordinateBounds(),
                EdgeInsets(pad, pad, pad, pad),
                map.cameraState.bearing,
                map.cameraState.pitch,
            ),
        )
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
        mpCameraPosition ?: return
        map.setCamera(mpCameraPosition.toCameraOptions())
    }

    override val cameraPosition: MPCameraPosition
        get() = map.cameraState.let { state ->
            MPCameraPosition {
                target = state.center.toMPLatLng()
                zoom = state.zoom.toFloat()
                bearing = state.bearing.toFloat()
                tilt = state.pitch.toFloat()
            }
        }

    override val projection: MPIProjection
        get() = MPProjection(map, mapView)

    override var isCompassEnabled: Boolean
        get() = mapView.compass.enabled
        set(value) {
            mapView.compass.enabled = value
        }

    private fun MPCameraPosition.toCameraOptions(): CameraOptions =
        CameraOptions.Builder()
            .center(target.toPoint())
            .zoom(zoom.toDouble())
            .bearing(bearing.toDouble())
            .pitch(tilt.toDouble())
            .build()
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Init and isReady

Lets go back to the `MapFragment`, and begin filling out the methods. `awaitMap` suspends until the map is ready, so we complete a `CompletableDeferred` once we have the platform map:

{% tabs %}
{% tab title="Google Maps" %}

On Google Maps we use the onMapReady method to get a `GoogleMap` instance after we call `getMapAsync` on our `MapView`, we then wrap the `GoogleMap` in our `Map` implementation `MIGoogleMap`, and complete `miMap` so `awaitMap` returns it.

{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
// Inside GoogleMapFragment
private lateinit var mapView: MapView
private lateinit var googleMap: GoogleMap
private val miMap = CompletableDeferred<MIGoogleMap>()

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mapView = view.findViewById(R.id.mapView)
    mapView.onCreate(savedInstanceState)
    mapView.getMapAsync(this)
}

override fun onMapReady(googleMap: GoogleMap) {
    mapView.onStart()
    this.googleMap = googleMap
    miMap.complete(MIGoogleMap(googleMap))
}

override suspend fun awaitMap(): MIGoogleMap = miMap.await()
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}

On Mapbox we load up the `MapView` and get the `MapboxMap` from it, which we then wrap in our `Map` implementation `MIMapboxMap`, once that is done we complete `miMap` so `awaitMap` returns it.

{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
// Inside MapboxMapFragment
private lateinit var mapView: MapView
private lateinit var mapboxMap: MapboxMap
private val miMap = CompletableDeferred<MIMapboxMap>()

override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mapView = view.findViewById(R.id.mapView)
    mapboxMap = mapView.mapboxMap
    mapboxMap.loadStyle(Style.MAPBOX_STREETS)
    miMap.complete(MIMapboxMap(mapboxMap, mapView))
}

override suspend fun awaitMap(): MIMapboxMap = miMap.await()
```
{% endcode %}
{% endtab %}
{% endtabs %}

### MapConfigBuilder

The `MapConfigBuilder` is quite straigthforward, but it needs to be implemented in the Fragment to keep the app completely platform independant.

To create a MPMapConfig we usually need the `Activity`, the platform `Map`, a platform `secret` key, and the `MapView`.

{% tabs %}
{% tab title="Google Maps" %}
{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
// Inside GoogleMapFragment
override fun getMapConfigBuilder(): MPMapConfig.Builder =
    MPMapConfig.Builder(requireActivity(), googleMap, getString(R.string.google_maps_key), mapView, true)
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}
{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
// Inside MapboxMapFragment
override fun getMapConfigBuilder(): MPMapConfig.Builder =
    MPMapConfig.Builder(requireActivity(), mapboxMap, mapView, getString(R.string.mapbox_api_key), true)
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Final class

This is how our `MapFragment` with `MIMap` is going to look like:

{% tabs %}
{% tab title="Google Maps" %}
{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import android.os.Bundle
import android.view.View
import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.MapView
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.googlemaps.MPMapConfig
import com.mapsindoors.googlemaps.MPProjection
import com.mapsindoors.googlemaps.converters.toCameraPosition
import com.mapsindoors.googlemaps.converters.toLatLngBounds
import com.mapsindoors.googlemaps.converters.toMPCameraPosition
import kotlinx.coroutines.CompletableDeferred

class GoogleMapFragment : MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {
    private lateinit var mapView: MapView
    private lateinit var googleMap: GoogleMap
    private val miMap = CompletableDeferred<MIGoogleMap>()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        mapView = view.findViewById(R.id.mapView)
        mapView.onCreate(savedInstanceState)
        mapView.getMapAsync(this)
    }

    override fun onMapReady(googleMap: GoogleMap) {
        mapView.onStart()
        this.googleMap = googleMap
        miMap.complete(MIGoogleMap(googleMap))
    }

    override suspend fun awaitMap(): MIGoogleMap = miMap.await()

    override fun getMapConfigBuilder(): MPMapConfig.Builder =
        MPMapConfig.Builder(requireActivity(), googleMap, getString(R.string.google_maps_key), mapView, true)

    class MIGoogleMap(private val map: GoogleMap) : MIMap {
        override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
            // sometimes the cameraPosition we create is nullable, lets handle it here instead of outside the method
            mpCameraPosition ?: return
            map.animateCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()))
        }

        override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
            mpCameraPosition ?: return
            map.animateCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()), duration, null)
        }

        override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
            latLngBounds ?: return
            map.moveCamera(CameraUpdateFactory.newLatLngBounds(latLngBounds.toLatLngBounds(), padding))
        }

        override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
            mpCameraPosition ?: return
            map.moveCamera(CameraUpdateFactory.newCameraPosition(mpCameraPosition.toCameraPosition()))
        }

        override val cameraPosition: MPCameraPosition
            get() = map.cameraPosition.toMPCameraPosition()

        override val projection: MPIProjection
            get() = MPProjection(map.projection, map.cameraPosition.zoom, map.cameraPosition.bearing)

        override var isCompassEnabled: Boolean
            get() = map.uiSettings.isCompassEnabled
            set(value) {
                map.uiSettings.isCompassEnabled = value
            }
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}
{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import android.os.Bundle
import android.view.View
import com.mapbox.maps.CameraOptions
import com.mapbox.maps.EdgeInsets
import com.mapbox.maps.MapView
import com.mapbox.maps.MapboxMap
import com.mapbox.maps.Style
import com.mapbox.maps.plugin.animation.MapAnimationOptions
import com.mapbox.maps.plugin.animation.flyTo
import com.mapbox.maps.plugin.compass.compass
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.mapbox.MPMapConfig
import com.mapsindoors.mapbox.MPProjection
import com.mapsindoors.mapbox.converters.toCoordinateBounds
import com.mapsindoors.mapbox.converters.toMPLatLng
import com.mapsindoors.mapbox.converters.toPoint
import kotlinx.coroutines.CompletableDeferred

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {
    private lateinit var mapView: MapView
    private lateinit var mapboxMap: MapboxMap
    private val miMap = CompletableDeferred<MIMapboxMap>()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        mapView = view.findViewById(R.id.mapView)
        mapboxMap = mapView.mapboxMap
        mapboxMap.loadStyle(Style.MAPBOX_STREETS)
        miMap.complete(MIMapboxMap(mapboxMap, mapView))
    }

    override suspend fun awaitMap(): MIMapboxMap = miMap.await()

    override fun getMapConfigBuilder(): MPMapConfig.Builder =
        MPMapConfig.Builder(requireActivity(), mapboxMap, mapView, getString(R.string.mapbox_api_key), true)

    class MIMapboxMap(private val map: MapboxMap, private val mapView: MapView) : MIMap {
        override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
            mpCameraPosition ?: return
            map.flyTo(mpCameraPosition.toCameraOptions())
        }

        override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
            mpCameraPosition ?: return
            map.flyTo(
                mpCameraPosition.toCameraOptions(),
                MapAnimationOptions.Builder().duration(duration.toLong()).build(),
            )
        }

        override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
            latLngBounds ?: return
            val pad = padding.toDouble()
            map.setCamera(
                map.cameraForCoordinateBounds(
                    latLngBounds.toCoordinateBounds(),
                    EdgeInsets(pad, pad, pad, pad),
                    map.cameraState.bearing,
                    map.cameraState.pitch,
                ),
            )
        }

        override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
            mpCameraPosition ?: return
            map.setCamera(mpCameraPosition.toCameraOptions())
        }

        override val cameraPosition: MPCameraPosition
            get() = map.cameraState.let { state ->
                MPCameraPosition {
                    target = state.center.toMPLatLng()
                    zoom = state.zoom.toFloat()
                    bearing = state.bearing.toFloat()
                    tilt = state.pitch.toFloat()
                }
            }

        override val projection: MPIProjection
            get() = MPProjection(map, mapView)

        override var isCompassEnabled: Boolean
            get() = mapView.compass.enabled
            set(value) {
                mapView.compass.enabled = value
            }

        private fun MPCameraPosition.toCameraOptions(): CameraOptions =
            CameraOptions.Builder()
                .center(target.toPoint())
                .zoom(zoom.toDouble())
                .bearing(bearing.toDouble())
                .pitch(tilt.toDouble())
                .build()
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Views

### Main Activity view

The `MainAcitivty` only needs a single `FragmentContainerView`, which in this case fills the entire parent.

{% code title="activity_main.xml" overflow="wrap" lineNumbers="true" %}
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/map_fragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```
{% endcode %}

### [Fragment views](#views)

The actual fragments contains a `Mapview` inside a `FrameLayout`.

{% tabs %}
{% tab title="Google Maps" %}
{% code title="google_map_fragment.xml" overflow="wrap" lineNumbers="true" %}
```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <com.google.android.gms.maps.MapView
        android:id="@+id/mapView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</FrameLayout>
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}
{% code title="mapbox_map_fragment.xml" overflow="wrap" lineNumbers="true" %}
```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <com.mapbox.maps.MapView
        android:id="@+id/mapView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapbox_cameraZoom="18.0" />
</FrameLayout>
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Secrets

Your platform keys should be kept safely in your resources where they can be fetched during runtime.

{% code title="secrets.xml" overflow="wrap" lineNumbers="true" %}
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="mapbox_api_key" translatable="false">YOUR_MAPBOX_API_KEY</string>
    <string name="mapbox_access_token" translatable="false">YOUR_MAPBOX_ACCESS_TOKEN</string>
    <string name="google_maps_key" translatable="false">YOUR_GOOGLE_MAPS_KEY</string>
</resources>
```
{% endcode %}

## Use in App

Now we have everything we need to set up the App. Here the approach is quite straightforward, we have made a method `buildConfig` which takes the name of a platform and a MapsIndoors API key, builds the appropriate `MapFragment`, and loads `MapsIndoors` as well.

When `awaitMap` returns, we call `initMapControl` which starts building a `MapControl` using the fragments' `MPMapConfig`, which we can extend in this example to also enable blue dot positioning.

Once `MapControl` has been create we use it to get the default venue, then use our `animateCamera` method to move the camera to the venue bounds.

If we call `buildConfig` again, but with a different platform, we will reload the solution, and use the other platform, all without making a new app!

We can even move the `MapsIndoors.load()` out of the `buildConfig` method, and switch platform without reloading the solution.

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import android.os.Bundle
import android.util.Log
import androidx.fragment.app.FragmentActivity
import androidx.lifecycle.lifecycleScope
import com.mapsindoors.core.MapControl
import com.mapsindoors.core.MapsIndoors
import com.mapsindoors.core.models.MPCameraPosition
import kotlinx.coroutines.launch

class MainActivity : FragmentActivity() {
    private var mapControl: MapControl? = null
    private lateinit var mapFragment: MapFragment

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        buildConfig(Provider.Mapbox, "mapspeople3d")
    }

    private fun buildConfig(provider: Provider, apiKey: String) {
        mapFragment = when (provider) {
            Provider.Google -> GoogleMapFragment()
            Provider.Mapbox -> MapboxMapFragment()
        }
        supportFragmentManager.beginTransaction().add(R.id.map_fragment, mapFragment).commit()
        MapsIndoors.load(applicationContext, apiKey) { _, error ->
            if (error != null) {
                Log.e("MainActivity", "Could not load MapsIndoors: ${error.message}")
            }
        }
        lifecycleScope.launch {
            initMapControl(mapFragment.awaitMap())
        }
    }

    private fun initMapControl(map: MIMap) {
        val mapConfig = mapFragment.getMapConfigBuilder().setShowUserPosition(true).build()
        MapControl.create(mapConfig) { mapControl, error ->
            if (mapControl == null) {
                Log.e("MainActivity", "Could not create MapControl: ${error?.message}")
                return@create
            }
            this.mapControl = mapControl
            val venue = MapsIndoors.venues?.defaultVenue ?: return@create
            val venueCenter = venue.bounds?.center ?: return@create
            // The callback can arrive on a background thread, so move the camera from the main thread
            lifecycleScope.launch {
                mapControl.selectFloor(venue.defaultFloor)
                map.animateCamera(
                    MPCameraPosition {
                        target = venueCenter
                        zoom = 17f
                    },
                )
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        mapControl?.onDestroy()
    }
}

enum class Provider {
    Mapbox,
    Google,
}
```
{% endcode %}

## Final words

We now know how to create an interface for using multiple platforms in the same app for MapsIndoors.