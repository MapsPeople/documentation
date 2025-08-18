# Using multiple Map Providers

It is possible to use `MapsIndoors` with multiple map providers, from this point we will call them platforms, like `Mapbox` and `Google Maps`, in the same app. The basics of this approach is to create a generic interface for accessing the maps, as well as provide some utility in setting up `MapsIndoors`.

In this article we will: Install multiple platforms, create a `Map` interface, create a `Fragment` interface to hold the `MapView`s, create implementations of the `Map` interface for each platform, and then blend them together in a single app.

We will use `Google Maps` and `Mapbox` as examples.

## Build gradle

First we have to add both platforms to our build file, it is important that both versions of MapsIndoors use the same version, otherwise we might experience issue with the interface.

{% code title="build.gradle" overflow="wrap" lineNumbers="true" %}

```gradle
ext {
  mapsindoors = "4.12.1"
  google_maps = "18.1.0"
  mapbox = "11.11.0"
}
dependencies {
  implementation "com.mapspeople.mapsindoors:mapbox-v11:$mapsindoors"
  implementation "com.mapspeople.mapsindoors:googlemaps:$mapsindoors"
  implementation "com.mapbox.maps:android:$mapbox"
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

The map interface should be as lean as possible, only containing the methods you need to use on the maps, in this example we have added 3 methods for moving the camera, as well the ability to read the current position, projection as well as turning the compass on/off.

This interface can be extended or shrunk to your hearts content, but remember that each method added will have to be implemented on both platforms.

{% code title="MIMap.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition

interface MIMap {
  fun animateCamera(mpCameraPosition: MPCameraPosition?)
  fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int)
  fun moveCamera(mpCameraPosition: MPCameraPosition?)
  val cameraPosition: MPCameraPosition?
  val projection: MPIProjection?
  var isCompassEnabled: Boolean
}
```

{% endcode %}

## MapFragment Class

The `MapFragment` is a container for the platforms' `MapView`s, it should be able to tell when the `MapView` is ready for content, be able to create a Map instance for us, and create the start of a `MPMapConfig` which ensure the app can remain ignorant of the platform implementations.

First we create the interface we should follow, it should implement the `Fragment` class so that we can manage it like a fragment.

### Interface

{% code title="MapFragment.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.annotation.LayoutRes
import androidx.fragment.app.Fragment
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

abstract class MapFragment(@LayoutRes contentLayoutId: Int) : Fragment(contentLayoutId) {
  abstract fun isReady(listener: OnResultReadyListener)
  abstract fun getMap(): MIMap
  abstract fun getMapConfigBuilder(): MPIMapConfig.Builder
}
```

{% endcode %}

### Map Fragment

Then we create the platform implementations, but lets just leave actual implementations blank for now, and focus on creating the `Map` implementation.

{% tabs %}
{% tab title="Google Maps" %}

For Google Maps we also implement the `OnMapCallback` to get a callback when the map is ready for use.

Go [here](#views) to see how the fragment layout is made.

{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.MapsInitializer
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

class GoogleMapFragment: MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {
  override fun isReady(listener: OnResultReadyListener) {
    TODO("Not yet implemented")
  }

  override fun getMap(): MIMap {
    TODO("Not yet implemented")
  }

  override fun getMapConfigBuilder(): MPIMapConfig.Builder {
    TODO("Not yet implemented")
  }

  override fun onMapReady(p0: GoogleMap) {
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
import com.mapsindoors.core.OnResultReadyListener

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {
  override fun isReady(listener: OnResultReadyListener) {
    TODO("Not yet implemented")
  }

  override fun getMap(): MIMap {
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

We create the `Map` implementation as a internal class in the `MapFragment`, this helps encapsulate it, as well as keeping all the platform implementation in a single file.

{% tabs %}
{% tab title="Google Maps" %}
{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIProjection
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.googlemaps.converters.toCameraPosition
import com.mapsindoors.googlemaps.converters.toLatLngBounds
import com.mapsindoors.googlemaps.converters.toMPLatLng

class GoogleMapFragment: MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {

  class MIGoogleMap(private val map : GoogleMap) : MIMap {
    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
      // sometimes the cameraPosition we create is nullable, lets handle it here instead of outside the method
      mpCameraPosition ?: return
      map.animateCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        )
      )
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
      mpCameraPosition ?: return
      map.animateCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        ), 500, null
      )
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
      latLngBounds ?: return
      map.moveCamera(
        CameraUpdateFactory.newLatLngBounds(
          latLngBounds.toLatLngBounds(), 10
        )
      )
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition ?: return
      map.moveCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        )
      )
    }

    override val cameraPosition: MPCameraPosition?
      get() = MPCameraPosition.Builder()
        .setBearing(map.cameraPosition.bearing)
        .setTarget(
          map.cameraPosition.target.toMPLatLng()
        )
        .setZoom(map.cameraPosition.zoom)
        .build()

    override val projection: MPIProjection?
      get() = MPProjection(
        map.projection,
        map.cameraPosition.zoom,
        map.maxZoomLevel
      )
    
    override var isCompassEnabled: Boolean
      get() = map.uiSettings.isCompassEnabled
      set(value) {map.uiSettings.isCompassEnabled = value}
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
import com.mapsindoors.testapp.R


class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {

  class MIMapboxMap(private val map: MapboxMap, private val mapView: MapView) : MIMap {
    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition?.let {
        map.flyTo(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build()
        )
      }
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
      mpCameraPosition?.let {
        map.flyTo(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build(),
          MapAnimationOptions.Builder().duration(duration.toLong()).build()
        )
      }
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
      latLngBounds?.let {
        val pad = padding.toDouble()
        map.setCamera(
          map.cameraForCoordinateBounds(
            it.toCoordinateBounds(),
            EdgeInsets(pad, pad, pad, pad),
            map.cameraState.bearing,
            map.cameraState.pitch
          )
        )
      }
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition?.let {
        map.setCamera(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build()
        )
      }
    }

    override val cameraPosition: MPCameraPosition?
      get() = map.cameraState.let {
        MPCameraPosition.Builder()
          .setZoom(it.zoom.toFloat())
          .setTarget(it.center.toMPLatLng())
          .setBearing(it.bearing.toFloat())
          .setTilt(it.pitch.toFloat())
          .build()
      }

    override val projection: MPIProjection?
      get() = MPProjection(map, mapView)

    override var isCompassEnabled: Boolean
      get() = mapView.compass.enabled
      set(value) {
        mapView.compass.enabled = value
      }
  }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Init and isReady

Lets go back to the `MapFragment`, and begin filling out the methods:

{% tabs %}
{% tab title="Google Maps" %}

On Google Maps we use the onMapReady method to get a `GoogleMap` instance after we call `getMapAsync` on our `MapView`, we then wrap the `GoogleMap` in our `Map` implementation `MIGoogleMap`, and inform the app via `isReady` that the `MapView` is ready.

{% code title="GoogleMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.MapsInitializer
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

class GoogleMapFragment: MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {
  private lateinit var mMapView: MapView
  private lateinit var mMap: GoogleMap
  private lateinit var miMap: MIGoogleMap
  private var ready: Boolean = false
  private var listener: OnResultReadyListener? = null

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mMapView = requireActivity().findViewById(R.id.mapView)
    mMapView.onCreate(savedInstanceState)
    mMapView.getMapAsync(this)
  }

  override fun onMapReady(p0: GoogleMap) {
    mMapView.onStart()
    mMap = p0
    miMap = MIGoogleMap(p0)
    ready = true
    listener?.onResultReady(null)
  }

  override fun isReady(listener: OnResultReadyListener) {
    if (ready) {
      listener.onResultReady(null)
    } else {
      this.listener = listener
    }
  }

  override fun getMap(): MIGoogleMap {
    return miMap
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}

On Mapbox we load up the `MapView` and get the `MapboxMap` from it, which we then wrap in our `Map` implementation `MIMapboxMap`, once that is done we update the `isReady` field and trigger the `isReady` listener to inform the app that the `MapView` is ready.

{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {
  private lateinit var mMapView: MapView
  private lateinit var mMap: MapboxMap
  private lateinit var miMap: MIMapboxMap
  private var isReady: Boolean = false
  private var readyListener: OnResultReadyListener? = null
  private val readyLock: Any = Any()

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mMapView = requireActivity().findViewById(R.id.mapView)
    mMap = mMapView.mapboxMap
    mMap.loadStyle(Style.MAPBOX_STREETS)
    miMap = MIMapboxMap(mMap, mMapView)
    synchronized(readyLock) {
      isReady = true
      readyListener?.onResultReady(null)
    }
  }

  override fun isReady(listener: OnResultReadyListener) {
    synchronized(readyLock) {
      if (isReady) {
        listener.onResultReady(null)
      } else {
        this.readyListener = listener
      }
    }
  }

  override fun getMap(): MIMapboxMap {
    return miMap
  }
}
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
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.MapsInitializer
import com.google.android.gms.maps.OnMapReadyCallback
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

class GoogleMapFragment: MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {

  override fun getMapConfigBuilder(): MPIMapConfig.Builder {
    return MPMapConfig.Builder(requireActivity(), mMap, getString(R.string.google_maps_key), mMapView, true)
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="Mapbox" %}
{% code title="MapboxMapFragment.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import com.mapsindoors.core.MPIMapConfig
import com.mapsindoors.core.OnResultReadyListener

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {

  override fun getMapConfigBuilder(): MPIMapConfig.Builder {
    return MPMapConfig.Builder(requireActivity(), mMap, mMapView, getString(R.string.mapbox_api_key), true)
  }
}
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
import com.mapsindoors.core.OnResultReadyListener
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.googlemaps.MPMapConfig
import com.mapsindoors.googlemaps.MPProjection
import com.mapsindoors.googlemaps.converters.toCameraPosition
import com.mapsindoors.googlemaps.converters.toLatLngBounds
import com.mapsindoors.googlemaps.converters.toMPLatLng

class GoogleMapFragment : MapFragment(R.layout.google_map_fragment), OnMapReadyCallback {
  private lateinit var mMapView: MapView
  private lateinit var mMap: GoogleMap
  private lateinit var miMap: MIGoogleMap
  private var ready: Boolean = false
  private var listener: OnResultReadyListener? = null

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mMapView = requireActivity().findViewById(R.id.mapView)
    mMapView.onCreate(savedInstanceState)
    mMapView.getMapAsync(this)
  }

  override fun onMapReady(p0: GoogleMap) {
    mMapView.onStart()
    mMap = p0
    miMap = MIGoogleMap(p0)
    ready = true
    listener?.onResultReady(null)
  }

  override fun isReady(listener: OnResultReadyListener) {
    if (ready) {
      listener.onResultReady(null)
    } else {
      this.listener = listener
    }
  }

  override fun getMap(): MIGoogleMap {
    return miMap
  }

  override fun getMapConfigBuilder(): MPMapConfig.Builder {
    return MPMapConfig.Builder(requireActivity(), mMap, getString(R.string.google_maps_key), mMapView, true)
  }

  class MIGoogleMap(private val map : GoogleMap) : MIMap {

    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition ?: return
      map.animateCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        )
      )
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
      mpCameraPosition ?: return
      map.animateCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        ), 500, null
      )
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
      latLngBounds ?: return
      map.moveCamera(
        CameraUpdateFactory.newLatLngBounds(
          latLngBounds.toLatLngBounds(), 10
        )
      )
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition ?: return
      map.moveCamera(
        CameraUpdateFactory.newCameraPosition(
          mpCameraPosition.toCameraPosition()
        )
      )
    }

    override val cameraPosition: MPCameraPosition?
      get() = MPCameraPosition.Builder()
        .setBearing(map.cameraPosition.bearing)
        .setTarget(
          map.cameraPosition.target.toMPLatLng()
        )
        .setZoom(map.cameraPosition.zoom)
        .build()

    override val projection: MPIProjection?
      get() = MPProjection(
        map.projection,
        map.cameraPosition.zoom,
        map.maxZoomLevel
      )
    override var isCompassEnabled: Boolean
      get() = map.uiSettings.isCompassEnabled
      set(value) {map.uiSettings.isCompassEnabled = value}
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
import com.mapsindoors.core.OnResultReadyListener
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.core.models.MPLatLngBounds
import com.mapsindoors.mapbox.MPMapConfig
import com.mapsindoors.mapbox.MPProjection
import com.mapsindoors.mapbox.converters.toCoordinateBounds
import com.mapsindoors.mapbox.converters.toMPLatLng
import com.mapsindoors.mapbox.converters.toPoint

class MapboxMapFragment : MapFragment(R.layout.mapbox_map_fragment) {
  private lateinit var mMapView: MapView
  private lateinit var mMap: MapboxMap
  private lateinit var miMap: MIMapboxMap
  private var isReady: Boolean = false
  private var readyListener: OnResultReadyListener? = null
  private val readyLock: Any = Any()

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    mMapView = requireActivity().findViewById(R.id.mapView)
    mMap = mMapView.mapboxMap
    mMap.loadStyle(Style.MAPBOX_STREETS)
    miMap = MIMapboxMap(mMap, mMapView)
    synchronized(readyLock) {
      isReady = true
      readyListener?.onResultReady(null)
    }
  }

  override fun isReady(listener: OnResultReadyListener) {
    synchronized(readyLock) {
      if (isReady) {
        listener.onResultReady(null)
      } else {
        this.readyListener = listener
      }
    }
  }

  override fun getMap(): MIMapboxMap {
    return miMap
  }

  override fun getMapConfigBuilder(): MPMapConfig.Builder {
    return MPMapConfig.Builder(requireActivity(), mMap, mMapView, getString(R.string.mapbox_api_key), true)
  }


  class MIMapboxMap(private val map: MapboxMap, private val mapView: MapView) : MIMap {
    override fun animateCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition?.let {
        map.flyTo(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build()
        )
      }
    }

    override fun animateCamera(mpCameraPosition: MPCameraPosition?, duration: Int) {
      mpCameraPosition?.let {
        map.flyTo(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build(),
          MapAnimationOptions.Builder().duration(duration.toLong()).build()
        )
      }
    }

    override fun moveCamera(latLngBounds: MPLatLngBounds?, padding: Int) {
      latLngBounds?.let {
        val pad = padding.toDouble()
        map.setCamera(
          map.cameraForCoordinateBounds(
            it.toCoordinateBounds(),
            EdgeInsets(pad, pad, pad, pad),
            map.cameraState.bearing,
            map.cameraState.pitch
          )
        )
      }
    }

    override fun moveCamera(mpCameraPosition: MPCameraPosition?) {
      mpCameraPosition?.let {
        map.setCamera(
          CameraOptions.Builder()
            .center(it.target.toPoint())
            .zoom(it.zoom.toDouble())
            .bearing(it.bearing.toDouble())
            .pitch(it.tilt.toDouble())
            .build()
        )
      }
    }

    override val cameraPosition: MPCameraPosition?
      get() = map.cameraState.let {
        MPCameraPosition.Builder()
          .setZoom(it.zoom.toFloat())
          .setTarget(it.center.toMPLatLng())
          .setBearing(it.bearing.toFloat())
          .setTilt(it.pitch.toFloat())
          .build()
      }

    override val projection: MPIProjection?
      get() = MPProjection(map, mapView)
    override var isCompassEnabled: Boolean
      get() = mapView.compass.enabled
      set(value) {
        mapView.compass.enabled = value
      }
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

Now we have everything we need to set up the App. Here the approach is quite straightforward, we have made a method `buildConfig` which takes the name of a platform, an MapsIndoors API key, and an error listener, and builds the appropriate `MapFragment`, and loads `MapsIndoors` as well.

When the fragment is ready, we call `initMapControl` which starts building a `MapControl` using the fragments' `MPMapConfig`, which we can extend in this example to also enable blue dot positioning.

Once `MapControl` has been create we use it to get the default venue, then use our `animateCamera` method to move the camera to the venue bounds.

If we call `buildConfig` again, but with a different platform, we will reload the solution, and use the other platform, all without making a new app!

We can even move the `MapsIndoors.load()` out of the `buildConfig` method, and switch platform without reloading the solution.

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}
```kotlin
import android.os.Build
import android.os.Bundle
import androidx.annotation.RequiresApi
import androidx.fragment.app.FragmentActivity
import com.mapsindoors.core.MPDebugLog
import com.mapsindoors.core.MapControl
import com.mapsindoors.core.MapsIndoors
import com.mapsindoors.core.OnMapsIndoorsReadyListener
import com.mapsindoors.core.OnResultReadyListener
import com.mapsindoors.core.errors.MIError
import com.mapsindoors.core.models.MPCameraPosition
import com.mapsindoors.testapp.Provider.Google
import com.mapsindoors.testapp.Provider.Mapbox
import com.mapsindoors.testapp.providers.GoogleMapFragment
import com.mapsindoors.testapp.providers.MIMap
import com.mapsindoors.testapp.providers.MapFragment
import com.mapsindoors.testapp.providers.MapboxMapFragment

class MainActivity : FragmentActivity(), OnResultReadyListener {
  private var mMapControl: MapControl? = null
  private lateinit var mFragment: MapFragment
  private lateinit var mMIMap: MIMap

  @RequiresApi(api = Build.VERSION_CODES.M)
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    buildConfig(Mapbox, "mapspeople3d") { e: MIError? -> }
  }

  override fun onResultReady(error: MIError?) {
    mMIMap = mFragment.getMap()
    initMapControl()
  }

  private fun initMapControl() {
    val mapConfig = mFragment.getMapConfigBuilder().setShowUserPosition(true).build()
    MapControl.create(mapConfig) { mapControl: MapControl?, miError: MIError? ->
      if (miError == null) {
        mMapControl = mapControl
        val venue = (MapsIndoors.getVenues() ?: return@create).defaultVenue
        runOnUiThread {
          if (venue != null) {
            mMapControl?.selectFloor(venue.defaultFloor)
            mMIMap.animateCamera(MPCameraPosition.Builder().setTarget(venue.bounds?.center).setZoom(17f).build())
          }
        }
      }
    }
  }

  private fun buildConfig(provider: Provider, apiKey: String, listener: OnMapsIndoorsReadyListener) {
    mFragment = when (provider) {
      Google -> GoogleMapFragment()
      Mapbox -> MapboxMapFragment()
    }
    supportFragmentManager.beginTransaction().add(R.id.map_fragment, mFragment).commit()
    MapsIndoors.load(application, apiKey, listener)
    mFragment.isReady(this)
  }

  override fun onDestroy() {
    super.onDestroy()
    mFragment.onDestroy()
  }
}

enum class Provider {
  Mapbox, Google,
}
```
{% endcode %}

## Final words

We now know how to create an interface for using multiple platforms in the same app for MapsIndoors.