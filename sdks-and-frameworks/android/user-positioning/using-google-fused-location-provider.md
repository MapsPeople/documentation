# Using Google Fused Location Provider

To get started with Google Fused Location Provider, you need to create a positioning implementation which enables communicating the positions received from the API with the MapsIndoors SDK.

The Position Provider implementation exists at the customer application level, and needs to use the `MPPositionProvider` interface from the MapsIndoors SDK. The MapsIndoors SDK can then use the positioning results given by the given Position Provider, by assigning the Position Provider to the `MapsIndoors.positionProvider` property.

#### Implementing Google Fused Location Provider API​ <a href="#implementing-google-fused-location-provider-api" id="implementing-google-fused-location-provider-api"></a>

This Guide requires you to already have an activity that shows a MapsIndoors Map and to have the Google Play Services Location library dependency added to your project.

```gradle
dependencies {
    implementation 'com.google.android.gms:play-services-location:21.3.0'
}
```

We will start implementing the Fused Location position provider. Create a class called `GPSPositionProvider` that implements the `MPPositionProvider` interface from the MapsIndoors SDK, and create a constructor that takes a `Context` as parameter and create an instance of the FusedLocationProviderClient.

[GPSPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/GPSPositionProvider.kt#L8-L12)

```kotlin
class GPSPositionProvider(context: Context) : MPPositionProvider {
    private val fusedLocationClient: FusedLocationProviderClient = LocationServices.getFusedLocationProviderClient(context)
    private var latestPosition: MPPositionResultInterface? = null
}
```

We will start by implementing logic to each of the implemented methods from the `MPPositionProvider` interface.

[GPSPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/GPSPositionProvider.kt#L42-L52)

```kotlin
class GPSPositionProvider(context: Context) : MPPositionProvider {

    private val fusedLocationClient: FusedLocationProviderClient = LocationServices.getFusedLocationProviderClient(context)
    private val positionUpdateListeners = ArrayList<OnPositionUpdateListener>()
    private var latestPosition: MPPositionResultInterface? = null

    override fun addOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.add(listener)
    }

    override fun removeOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.remove(listener)
    }

    override fun getLatestPosition(): MPPositionResultInterface? = latestPosition
}
```

We will then start implementing the code to get Google Fused Location positioning up and running.

We start by implementing the `startPositioning` and `stopPositioning` methods:

[GPSPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/GPSPositionProvider.kt#L32-L40)

```kotlin
class GPSPositionProvider(context: Context) : MPPositionProvider {
    // ...

    fun startPositioning() {
        val locationRequest = LocationRequest.Builder(1000).build()
        // Without a Looper, the updates are delivered on the main thread
        fusedLocationClient.requestLocationUpdates(locationRequest, locationCallback, null)
    }

    fun stopPositioning() {
        fusedLocationClient.removeLocationUpdates(locationCallback)
    }
}
```

Implement the `LocationCallback` in the provider to receive the location updates, and report each one to the attached listeners.

[GPSPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/GPSPositionProvider.kt#32-L40)

```kotlin
class GPSPositionProvider(context: Context) : MPPositionProvider {
    // ...

    private val locationCallback = object : LocationCallback() {
        override fun onLocationResult(locationResult: LocationResult) {
            val location = locationResult.lastLocation ?: return
            val position = MPPositionResult(MPPoint(location.latitude, location.longitude), location.accuracy)
            position.provider = this@GPSPositionProvider
            latestPosition = position
            positionUpdateListeners.forEach { it.onPositionUpdate(position) }
        }
    }
}
```

Our implemented positioning provider will be handled in an activity or fragment.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L40-L54)

```kotlin
class MyFragment : Fragment() {
    private var positionProvider: GPSPositionProvider? = null

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
        // ...
        positionProvider = GPSPositionProvider(requireActivity())
        MapsIndoors.load(requireActivity().applicationContext, "myapikey") { _, _ ->
            // Attach the position provider to the SDK
            MapsIndoors.positionProvider = positionProvider
        }

        // Have a button or other logic to start positioning:
        binding.startPositioningButton.setOnClickListener {
            // Remember to handle permissions specific to the position provider you are using
            positionProvider?.startPositioning()
        }
        // ...
        return binding.root
    }
}
```

Lastly, we need to tell `MapControl` that we want to show the position on the map.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L93-L97)

```kotlin
MapControl.create(mapConfig) { mapControl, _ ->
    this.mapControl = mapControl

    // Enable showing the position indicator (aka. the blue dot)
    mapControl?.showUserPosition(true)
}
```

A full example implementation of the Google Fused Location position provider can be found here: [PositionProviders](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning)
