# Using Indoor Atlas

To get started with Indoor Atlas positioning, you need to create a positioning implementation which enables communicating the positions received from Indoor Atlas with the MapsIndoors SDK.

The Position Provider implementation exists at the customer application level, and needs to use the `MPPositionProvider` interface from the MapsIndoors SDK. The MapsIndoors SDK can then use the positioning results given by the given Position Provider, by setting the Position Provider with `MapsIndoors.setPositionProvider(MPPositionProvider)`.

#### Floor Mapping[​](https://docs.mapsindoors.com/indoor-atlas#floor-mapping) <a href="#floor-mapping" id="floor-mapping"></a>

The Position Provider should align with the MapsIndoors Floor index convention (floors are indexed as e.g 0, 10, 20, 30 corresponding to ground floor, 1st floor, 2nd floor, 3rd floor, with negative floors indices allowed as well to indicate Floors below ground, e.g. -10). It is therefore up to the Position Provider class to convert any given Floor indexing from the positioning source to that of MapsIndoors.

For a typical Position Provider, the mapping from the positioning's index needs to be mapped to the MapsIndoors Floor format. This is possible through the CMS or creating your own int:int mapping.

**Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/indoor-atlas#fetch-attributes-from-solution)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```kotlin
Map<String, Map<String, Object>> providerConfig = MapsIndoors.getSolution().getPositionProviderConfig();
```

The outer keyset (`Map<String, Map<String, Object>>`) contains the name of the positioning provider, for example, `indooratlas3` for IndoorAtlas, or `ciscodna` when using Cisco DNA Spaces.

The inner keyset (`Map<String, Object>`) consist of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.

#### Implementing Indoor Atlas[​](https://docs.mapsindoors.com/indoor-atlas#implementing-indoor-atlas) <a href="#implementing-indoor-atlas" id="implementing-indoor-atlas"></a>

This Guide requires you to already have an activity that shows a MapsIndoors Map as well as a Indoor Atlas beacon network for positioning. We use Indoor Atlas v3 for this guide. Here is how to set it up in your project: [Indoor Atlas setup](https://indooratlas.freshdesk.com/support/solutions/articles/36000050564-setup-positioning-sdk-with-android)

To begin, we will start implementing the Indoor Atlas position provider. Create a class called `IndoorAtlasPositionProvider` that implements the `MPPositionProvider` interface from the MapsIndoors SDK, and create a constructor that takes a `Context` as parameter. If you have IndoorAtlas setup through MapsIndoors you can use the MPIndoorAtlasConfig from your solution as a convenience on setup.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L8-L18)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig): MPPositionProvider {
    private var mLatestPosition: MPPositionResultInterface? = null
    private var mLatestBearing: Float? = null
    private var indoorAtlasClient: IALocationManager? = null
}
```

We will start by implementing methods from the `MPPositionProvider` interface.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L116-L126)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig): MPPositionProvider {
    ...
    override fun addOnPositionUpdateListener(p0: OnPositionUpdateListener) {
        positionUpdateListeners.add(p0)
    }

    override fun removeOnPositionUpdateListener(p0: OnPositionUpdateListener) {
        positionUpdateListeners.remove(p0)
    }

    override fun getLatestPosition(): MPPositionResultInterface? {
        return mLatestPosition
    }
}
```

We will then start implementing the code to get Indoor Atlas positioning up and running.

For Indoor Atlas to work you will need to supply Indoor Atlas with a API key and a secret key. This can be handled in two ways, if the Indoor Atlas account is setup through MapsPeople CMS on the Position Provider tab, we will have the data for this stored on the MapsIndoors SDK. If not you will have to handle the two keys yourself, this can be done through String resources as an example.

We start by creating a method to initiate the Indoor Atlas client. Here the method is called `initClient`.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L91-L97)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig): MPPositionProvider {
    ...
    private void initClient(){
        val extras = Bundle(2)
        extras.putString(IALocationManager.EXTRA_API_KEY, config.key)
        extras.putString(IALocationManager.EXTRA_API_SECRET, config.secret)

        indoorAtlasClient = IALocationManager.create(context, extras)
    }
    ...
}
```

Create a `IAOrientationListener` and `IALocationListener` we will register theese to the IndoorAtlas client when we start positioning. We will also implement a `notifyPositionUpdate` method to notify the Positionupdate Listeners of a position change.

[IndoorAtlasPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/IndoorAtlasPositionProvider.kt#L8-L89)

```kotlin
class IndoorAtlasPositionProvider(private val context: Context, private val config: MPIndoorAtlasConfig): MPPositionProvider {
    private var mLastHeadingUpdateTime: Long = 0
    private val MIN_TIME_BETWEEN_UPDATES_IN_MS: Long = 100
    private var mLatestBearing: Float? = null

    private val orientationListener: IAOrientationListener = object : IAOrientationListener {
        override fun onHeadingChanged(timestamp: Long, heading: Double) {
            mLatestPosition?.let {
                val dt: Long = timestamp - mLastHeadingUpdateTime

                if (dt < MIN_TIME_BETWEEN_UPDATES_IN_MS) {
                    return
                }

                mLastHeadingUpdateTime = timestamp

                it.bearing = heading.toFloat()
                mLatestBearing = it.bearing

                notifyPositionUpdate()
            }
        }

        override fun onOrientationChange(p0: Long, p1: DoubleArray?) {
            //Empty as we only use heading here
        }
    }

    private val locationListener = object : IALocationListener {
        override fun onLocationChanged(location: IALocation?) {
            location?.let {
                val lat = it.latitude
                val lng = it.longitude
                val floorLevel = it.floorLevel
                val accuracy = it.accuracy

                val hasFloorLevel = it.hasFloorLevel()

                val positionResult = MPPositionResult(MPPoint(lat, lng), accuracy)
                positionResult.androidLocation = it.toLocation()
                if (mLatestBearing != null) {
                    positionResult.bearing = mLatestBearing!!
                }
                //Notice we use a method from our IndoorAtlasConfig to retreive the correct MapsIndoors floor level from the IndoorAtlas result. If you do not have a config set up through the CMS,
                //You can create a method that handles the floor mapping itself.
                if (hasFloorLevel) {
                    if (config.getMappedFloorIndex(floorLevel) != null) {
                        positionResult.floorIndex = config.getMappedFloorIndex(floorLevel)!!
                    }else {
                        positionResult.floorIndex = MPFloor.DEFAULT_GROUND_FLOOR_INDEX
                    }
                }else {
                    positionResult.floorIndex = MPFloor.DEFAULT_GROUND_FLOOR_INDEX
                }

                positionResult.provider = this@IndoorAtlasPositionProvider

                mLatestPosition = positionResult
                notifyPositionUpdate()
            }
        }

        override fun onStatusChanged(p0: String?, p1: Int, p2: Bundle?) {
            //Blank for this, can be used to report if indoor atlas is unavailable etc.
        }
    }


    fun notifyPositionUpdate() {
        for (positionUpdateListener in positionUpdateListeners) {
            mLatestPosition?.let {
                positionUpdateListener.onPositionUpdate(it)
            }
        }
    }
}
```

Implement the `startPositoning` and `stopPositioning` method:

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
class myFragment: Fragment(), OnMapReadyCallback {
    private var mPositionProvider: IndoorAtlasPositionProvider? = null

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?,): View {
        ...
        MapsIndoors.load(requireActivity().applicationContext, "myapikey") {
            // Attach the position provider to the SDK
            mPositionProvider = IndoorAtlasPositionProvider(requireActivity(), MapsIndoors.getSolution()!!.indoorAtlasConfig!!)
            MapsIndoors.setPositionProvider(mPositionProvider)
        }

        //Have a buttton or other logic to start positioning:
        binding.startPositioningButton.setOnClickListener {
            //Remember to handle permissions specific to the Position provider you are using
            mPositionProvider?.startPositioning()
        }
        ...
    }
}
```

Lastly, we need to tell `MapControl` that we want to show the position on the map.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L93-L97)

```kotlin
MapControl.create(mapConfig) { mapControl: MapControl?, miError: MIError? ->
    mMapControl = mapControl

    // Enable showing the position indicator (aka. the blue dot)
    mMapControl?.showUserPosition(true)
}
```

A full example implementation of the Indoor Atlas position provider can be found here: [PositionProviders](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning)
