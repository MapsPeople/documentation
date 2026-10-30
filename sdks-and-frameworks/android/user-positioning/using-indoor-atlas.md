# Using Indoor Atlas

To get started with Indoor Atlas positioning, you need to create a positioning implementation which enables communicating the positions received from Indoor Atlas with the MapsIndoors SDK.

The Position Provider implementation exists at the customer application level, and needs to use the `MPPositionProvider` interface from the MapsIndoors SDK. The MapsIndoors SDK can then use the positioning results given by the given Position Provider, by assigning the Position Provider to the `MapsIndoors.positionProvider` property.

#### Floor Mapping​ <a href="#floor-mapping" id="floor-mapping"></a>

The Position Provider should align with the MapsIndoors Floor index convention (floors are indexed as e.g 0, 10, 20, 30 corresponding to ground floor, 1st floor, 2nd floor, 3rd floor, with negative floors indices allowed as well to indicate Floors below ground, e.g. -10). It is therefore up to the Position Provider class to convert any given Floor indexing from the positioning source to that of MapsIndoors.

For a typical Position Provider, the mapping from the positioning's index needs to be mapped to the MapsIndoors Floor format. This is possible through the CMS or creating your own int:int mapping.

**Fetch Attributes from Solution​**

You can choose to fetch the IndoorAtlas configuration (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```kotlin
val indoorAtlasConfig: MPIndoorAtlasConfig? = MapsIndoors.solution?.indoorAtlasConfig
```

`MPIndoorAtlasConfig` holds the IndoorAtlas `key` and `secret`, and `getMappedFloorIndex(floorIndex)` converts an IndoorAtlas floor number to a MapsIndoors Floor index using the floor mapping set up in the CMS. It is `null` if IndoorAtlas is not configured for the Solution.

#### Implementing Indoor Atlas​ <a href="#implementing-indoor-atlas" id="implementing-indoor-atlas"></a>

This Guide requires you to already have an activity that shows a MapsIndoors Map as well as a Indoor Atlas beacon network for positioning. We use Indoor Atlas v3 for this guide. Here is how to set it up in your project: [Indoor Atlas setup](https://indooratlas.freshdesk.com/support/solutions/articles/36000050564-setup-positioning-sdk-with-android)

To begin, we will start implementing the Indoor Atlas position provider. Create a class called `IndoorAtlasPositionProvider` that implements the `MPPositionProvider` interface from the MapsIndoors SDK, and create a constructor that takes a `Context` as parameter. If you have IndoorAtlas setup through MapsIndoors you can use the MPIndoorAtlasConfig from your solution as a convenience on setup.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L8-L18)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig) : MPPositionProvider {
    private val positionUpdateListeners = ArrayList<OnPositionUpdateListener>()
    private var latestPosition: MPPositionResultInterface? = null
    private var indoorAtlasClient: IALocationManager? = null
}
```

We will start by implementing methods from the `MPPositionProvider` interface.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L116-L126)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig) : MPPositionProvider {
    // ...

    override fun addOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.add(listener)
    }

    override fun removeOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.remove(listener)
    }

    override fun getLatestPosition(): MPPositionResultInterface? = latestPosition
}
```

We will then start implementing the code to get Indoor Atlas positioning up and running.

For Indoor Atlas to work you will need to supply Indoor Atlas with a API key and a secret key. This can be handled in two ways, if the Indoor Atlas account is setup through MapsPeople CMS on the Position Provider tab, we will have the data for this stored on the MapsIndoors SDK. If not you will have to handle the two keys yourself, this can be done through String resources as an example.

We start by creating a method to initiate the Indoor Atlas client. Here the method is called `initClient`.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L91-L97)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig) : MPPositionProvider {
    // ...

    private fun initClient() {
        val extras = Bundle(2)
        extras.putString(IALocationManager.EXTRA_API_KEY, config.key)
        extras.putString(IALocationManager.EXTRA_API_SECRET, config.secret)

        indoorAtlasClient = IALocationManager.create(context, extras)
    }

    // ...
}
```

Create an `IAOrientationListener` and an `IALocationListener`; we will register these to the IndoorAtlas client when we start positioning. We will also implement a `notifyPositionUpdate` method to notify the position update listeners of a position change. The MapsIndoors Floor index of a position is the z-coordinate of its `MPPoint`.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L8-L89)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig) : MPPositionProvider {
    private var lastHeadingUpdateTime: Long = 0
    private var latestBearing: Float? = null

    private val orientationListener = object : IAOrientationListener {
        override fun onHeadingChanged(timestamp: Long, heading: Double) {
            val position = latestPosition ?: return
            if (timestamp - lastHeadingUpdateTime < MIN_TIME_BETWEEN_UPDATES_IN_MS) {
                return
            }
            lastHeadingUpdateTime = timestamp

            position.bearing = heading.toFloat()
            latestBearing = position.bearing

            notifyPositionUpdate()
        }

        override fun onOrientationChange(timestamp: Long, quaternion: DoubleArray?) {
            // Empty as we only use heading here
        }
    }

    private val locationListener = object : IALocationListener {
        override fun onLocationChanged(location: IALocation?) {
            location ?: return
            // Notice we use a method from our MPIndoorAtlasConfig to retrieve the correct MapsIndoors floor index from the IndoorAtlas result.
            // If you do not have a config set up through the CMS, you can create a method that handles the floor mapping itself.
            val floorIndex = location.takeIf { it.hasFloorLevel() }?.let { config.getMappedFloorIndex(it.floorLevel) }
                ?: MPFloor.DEFAULT_GROUND_FLOOR_INDEX

            val point = MPPoint(location.latitude, location.longitude, floorIndex.toDouble())
            val positionResult = MPPositionResult(point, location.accuracy)
            positionResult.androidLocation = location.toLocation()
            positionResult.bearing = latestBearing
            positionResult.provider = this@IndoorAtlasPositionProvider

            latestPosition = positionResult
            notifyPositionUpdate()
        }

        override fun onStatusChanged(provider: String?, status: Int, extras: Bundle?) {
            // Blank for this, can be used to report if Indoor Atlas is unavailable etc.
        }
    }

    private fun notifyPositionUpdate() {
        val position = latestPosition ?: return
        positionUpdateListeners.forEach { it.onPositionUpdate(position) }
    }

    private companion object {
        const val MIN_TIME_BETWEEN_UPDATES_IN_MS = 100L
    }
}
```

Implement the `startPositioning` and `stopPositioning` methods:

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L99-L114)

```kotlin
fun startPositioning() {
    if (indoorAtlasClient == null) {
        initClient()
    }
    indoorAtlasClient?.registerOrientationListener(IAOrientationRequest(1.0, 0.0), orientationListener)
    indoorAtlasClient?.requestLocationUpdates(IALocationRequest.create(), locationListener)
    indoorAtlasClient?.lockIndoors(false)
}

fun stopPositioning() {
    indoorAtlasClient?.let {
        it.unregisterOrientationListener(orientationListener)
        it.removeLocationUpdates(locationListener)
        it.lockIndoors(true)
    }
}
```

Our implemented positioning provider will be handled in an activity or fragment.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L40-L54)

```kotlin
class MyFragment : Fragment() {
    private var positionProvider: IndoorAtlasPositionProvider? = null

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
        // ...
        val context = requireActivity()
        MapsIndoors.load(context.applicationContext, "myapikey") { _, _ ->
            // The IndoorAtlas configuration is null if IndoorAtlas is not set up for the Solution
            val config = MapsIndoors.solution?.indoorAtlasConfig ?: return@load
            // Attach the position provider to the SDK
            positionProvider = IndoorAtlasPositionProvider(context, config)
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

A full example implementation of the Indoor Atlas position provider can be found here: [PositionProviders](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning)
