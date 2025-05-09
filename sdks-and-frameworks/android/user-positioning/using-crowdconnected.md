# Using CrowdConnected

To get started with CrowdConnected positioning, you can either create your own implementation by using the `MPPositionProvider` interface from the MapsIndoors SDK to visualize position updates from CrowdConnected.

Alternatively, you can use the MapsIndoors CrowdConnected Compatibility Package (MCCP for short), which handles this work for you, requiring minimal setup. This guide shows how to implement MCCP.

This guide assumes you already have an app with MapsIndoors.

## Download maven package

First, download the Maven package and the CrowdConnected IPS:

{% code title="build.gradle.kts" overflow="wrap" lineNumbers="false" %}

```kts
dependencies {
  implementation("com.mapspeople.mapsindoors:mapsindoors-crowdconnected-positioning-provider:1.0.0")
  implementation("net.crowdconnected.android.core:android-core:2.0.2")
  implementation("net.crowdconnected.android.ips:android-ips:2.0.2")
}
```

And add the CrowdConnected maven url to your dependency resolution:

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

## Get Android permissions

Before positioning can be enabled, you must ask the user for permissions. The required permissions are: `ACCESS_FINE_LOCATION`, `BLUETOOTH_SCAN`, and `BLUETOOTH_CONNECT`.

You can decide where to request these permissions, it should be naturally integrated into the flow of opening the map or when the user interacts with the app to enable positioning themselves.

To request the permissions, it is easiest to use `ActivityCompat`. In this example, we request the permissions in our `MainActivity`'s `onCreate` method:

{% code title="MainActivity.kt" overflow="wrap" lineNumbers="true" %}

```kotlin
import androidx.core.app.ActivityCompat
import androidx.fragment.app.FragmentActivity

class MainActivity : FragmentActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION, BLUETOOTH_SCAN, BLUETOOTH_CONNECT ), 0)
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

class MainActivity : FragmentActivity(), ActivityCompat.OnRequestPermissionsResultCallback {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    ActivityCompat.requestPermissions(this, arrayOf(ACCESS_FINE_LOCATION, BLUETOOTH_SCAN, BLUETOOTH_CONNECT ), 0)
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
      MPCrowdConnectedConfig("your_app_key", "your_token", "your_secret") // you should not input your secrets directly, fetch them from somewhere secure
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
  }
}
```

{% endcode %}

In the above example we initialized the `positionManager` using a set of CrowdConnected secrets that we got from their CMS. It is optional to add a `StatusCallback`, in this example we added it for debug purposes, it will print to the log and create a `Toast` informing the status of initialization.

With this, positioning is up and running, and can be tested out in your app. The only thing we are missing is lifecycle handling.

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