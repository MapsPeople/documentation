# Using CrowdConnected

To get started with CrowdConnected positioning, you can either create your own implementation by using the `MPPositionProvider` interface from the MapsIndoors SDK to visualize position updates from CrowdConnected.

Alternatively, you can use the MapsIndoors CrowdConnected Positioning Provider, which handles this work for you, requiring minimal setup. This guide shows how to implement it.

This guide assumes you already have an app with MapsIndoors.

## Download maven package

First, add the CrowdConnected maven url to your dependency resolution:

{% code title="settings.gradle.kts" overflow="wrap" lineNumbers="false" %}

```kts
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
        maven { url = uri("https://maven.mapsindoors.com/") }
        maven { url = uri("https://maven2.crowdconnected.net/") }
    }
}
```

{% endcode %}

Then download the Maven package and the CrowdConnected IPS:

{% code title="build.gradle.kts" overflow="wrap" lineNumbers="false" %}

```kts
dependencies {
  implementation("com.mapspeople.mapsindoors:mapsindoors-crowdconnected-positioning-provider:1.0.0")
  implementation("net.crowdconnected.android.core:android-core:2.0.2")
  implementation("net.crowdconnected.android.ips:android-ips:2.0.2")
}
```

{% endcode %}

<!---
### Fork the project

If you prefer more control, want to understand how it works, or want to customize the positioning provider package, you can go to the publicly available GitHub project. The source code is hosted at [MapsIndoors CrowdConnected Package GitHub Repository](https://github.com/MapsPeople/mapsindoors_crowdconnected_positioning_provider) (Not currently available). By forking the repository, you create your own copy of the project, which you can modify to suit your specific requirements while retaining the base functionality.
--->

## Get Android permissions

Before positioning can be enabled, you must ask the user for permissions. The required permissions are: `ACCESS_FINE_LOCATION`, `BLUETOOTH_SCAN`, and `BLUETOOTH_CONNECT`. Apps below API level 31 should not request `BLUETOOTH_SCAN` and `BLUETOOTH_CONNECT` as these were added in 31.

You can decide where to request these permissions, it should be naturally integrated into the flow of opening the map or when the user interacts with the app to enable positioning themselves.

To request the permissions, it is easiest to use `ActivityCompat`. In this example, we request the permissions in our `MainActivity`'s `onCreate` method:

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.core.app.ActivityCompat
import androidx.fragment.app.FragmentActivity
import android.Manifest.permission.ACCESS_FINE_LOCATION
import android.Manifest.permission.BLUETOOTH_CONNECT
import android.Manifest.permission.BLUETOOTH_SCAN
import android.os.Build
import android.os.Bundle

class MainActivity : FragmentActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
      ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION, BLUETOOTH_SCAN, BLUETOOTH_CONNECT ), 0)
    } else {
      ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION), 0)
    }
  }
}
```

{% endcode %}

### Callback

If you want to await the user granting permissions, you must implement the `OnRequestPermissionsResultCallback` interface, as shown below:

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.core.app.ActivityCompat
import androidx.fragment.app.FragmentActivity
import android.content.pm.PackageManager.PERMISSION_GRANTED
import android.Manifest.permission.ACCESS_FINE_LOCATION
import android.Manifest.permission.BLUETOOTH_CONNECT
import android.Manifest.permission.BLUETOOTH_SCAN
import android.os.Build
import android.os.Bundle

class MainActivity : FragmentActivity(), ActivityCompat.OnRequestPermissionsResultCallback {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
      ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION, BLUETOOTH_SCAN, BLUETOOTH_CONNECT ), 0)
    } else {
      ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION), 0)
    }
  }

  override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String?>, grantResults: IntArray) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)

    if (grantResults.all { it == PERMISSION_GRANTED }) {
        // startPositioning()
    }
  }
}
```

{% endcode %}

## Set up `MPCrowdConnectedManager`

The positioning manager should only be initialized when positioning is required. It depends on the necessary permissions (mentioned above) and MapsIndoors being initialized.

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import android.util.Log
import android.widget.Toast
import androidx.fragment.app.FragmentActivity
import com.mapsindoors.positioning.crowdconnected.MPCrowdConnectedManager
import com.mapsindoors.positioning.crowdconnected.MPCrowdConnectedConfig
import net.crowdconnected.android.core.StatusCallback

class MainActivity : FragmentActivity() {
  private var positionManager: MPCrowdConnectedManager? = null

  fun startPositioning() {
    positionManager = MPCrowdConnectedManager(
      getApplication(),
      MPCrowdConnectedConfig("your_app_key", "your_token", "your_secret"), // you should not input your secrets directly, fetch them from somewhere secure
      object : StatusCallback {
        override fun onStartUpSuccess() {
          Log.i("IPS", "CrowdConnected started successfully")
          runOnUiThread {
            Toast.makeText(getApplicationContext(), "CrowdConnected started successfully", Toast.LENGTH_LONG).show()
          }
        }

        override fun onRuntimeError(error: String?) {
          Log.i("IPS", "CrowdConnected runtime error: $error")
          runOnUiThread {
            Toast.makeText(getApplicationContext(), "CrowdConnected runtime error: $error", Toast.LENGTH_LONG).show()
          }
        }

        override fun onStartUpFailure(failure: String?) {
          Log.i("IPS", "CrowdConnected startup failure: $failure")
          runOnUiThread {
            Toast.makeText(getApplicationContext(), "CrowdConnected startup failure: $failure", Toast.LENGTH_LONG).show()
          }
        }
      }
    )
    MapsIndoors.setPositionProvider(positionManager!!)
  }
}
```

{% endcode %}

In the above example we initialized the `positionManager` using a set of CrowdConnected secrets that we got from their CMS, you can follow the CrowdConnected guide on how to generate them [here](https://customer.support.crowdconnected.com/servicedesk/customer/portal/1/article/2888139205). It is optional to add a `StatusCallback`, in this example we added it for debug purposes, it will print to the log and create a `Toast` informing the status of initialization.

With this, positioning is up and running, and can be tested out in your app. The only thing we are missing is lifecycle handling.

## Show it on the map

The last step to get the positioning shown on the map is to enable it in `MapControl`. This is typically done when creating the `MapControl`, but it can be turned on at any time. In the following example we turn on positioning when creating our `MapControl`, by adding it to the `MPMapConfig`. The example is based on using Mapbox, but this can be done for any supported map.

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import android.os.Bundle
import androidx.fragment.app.FragmentActivity
import com.mapsindoors.core.MapControl
import com.mapsindoors.mapbox.MPMapConfig
import com.mapsindoors.core.errors.MIError
import com.mapbox.maps.MapView
import com.mapbox.maps.MapboxMap

class MainActivity : FragmentActivity() {
  private var mMapControl: MapControl? = null
  private var mMap: MapBoxMap? = null
  private var mMapView: MapBoxMapView? = null

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    // initialize Map
    mMapView = requireActivity().findViewById(R.id.mapView)
    mMap = mMapView.mapboxMap
    // start building MapControl
    initMapControl()
  }

  private fun initMapControl() {
    val mapConfig = MPMapConfig.Builder(this, mMap ?: return, mMapView ?: return, "your_mapbox_api_key", true)
      .setShowUserPosition(true) // setShowUserPosition to true to show the latest position given to MapsIndoors on the map
      .build()
    MapControl.create(mapConfig) { mapControl: MapControl?, miError: MIError? ->
      if (miError == null) {
        mMapControl = mapControl
      }
    }
  }
}
```

{% endcode %}

Positioning can also be enabled or disabled on `MapControl` at any time by calling `showUserPosition` on `MapControl`:

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.fragment.app.FragmentActivity
import com.mapsindoors.core.MapControl

class MainActivity : FragmentActivity() {
  private var mMapControl: MapControl? = null

  private fun showPosition(show: Boolean) {
    // positioning can be enabled at any time mapcontrol is accessible
    mMapControl?.showUserPosition(show)
  }
}
```
{% endcode %}

## Lifecycle

It is important to ensure that your positioning implementation respects the Android Lifecycle. The position manager should be properly started, paused, and resumed according to the activity lifecycle to prevent memory leaks or unexpected behavior.

How you integrate lifecycle management may vary based on your app’s architecture (e.g., using LifecycleObservers, ViewModel, or manual lifecycle management). Make sure to consider your app’s structure and user experience when deciding how to manage the lifecycle of your position manager.

Example

Here's a basic example of how you can manage lifecycle manually:

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.fragment.app.FragmentActivity
import com.mapsindoors.positioning.crowdconnected.MPCrowdConnectedManager

class MainActivity : FragmentActivity() {
  private var positionManager: MPCrowdConnectedManager? = null

  override fun onStop() {
    super.onStop()
    positionManager?.stop()
  }

  override fun onResume() {
    super.onResume()
    positionManager?.restart()
  }
}
```

{% endcode %}