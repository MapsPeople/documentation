# Using Cisco DNA Spaces

To get started with Cisco DNA positioning, the MapsIndoors SDK offers all the required building blocks without any external dependencies.

The Position Provider implementation exists at the customer application level, and needs to use the `MPPositionProvider` interface from the MapsIndoors SDK. The MapsIndoors SDK can then use the positioning results given by the Position Provider when it is assigned to the `MapsIndoors.positionProvider` property.

#### Floor Mapping for Android​ <a href="#floor-mapping-for-android" id="floor-mapping-for-android"></a>

The Position Provider should align with the MapsIndoors Floor index convention (floors are indexed as e.g 0, 10, 20, 30 corresponding to ground floor, 1st floor, 2nd floor, 3rd floor, with negative floors indices allowed as well to indicate Floors below ground, e.g. -10). It is therefore up to the Position Provider class to convert any given Floor indexing from the positioning source to that of MapsIndoors.

For a typical Position Provider, the mapping from the positioning's index needs to be mapped to the MapsIndoors Floor format.

The MapsIndoors backend is closely integrated with the CiscoDNA platform, so the MapsIndoors backend handles the floor mapping conversion for that integration. From an application perspective no Floor mapping implementation is required when integrating CiscoDNA positioning through the MapsIndoors platform.

**Fetch Attributes from Solution​**

You can choose to fetch the Cisco DNA configuration (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```kotlin
val ciscoConfig: MPCiscoDNAConfig? = MapsIndoors.solution?.ciscoDNAConfig
```

`MPCiscoDNAConfig` holds the `tenantId` needed to set up Cisco DNA positioning. It is `null` if Cisco DNA is not configured for the Solution.

#### Implementing Cisco DNA for Android​ <a href="#implementing-cisco-dna-for-android" id="implementing-cisco-dna-for-android"></a>

This Guide requires you to already have an activity that shows a MapsIndoors Map as well as a Cisco DNA network with positioning active.

The example provider below makes its network requests with [OkHttp](https://square.github.io/okhttp/) and parses responses with [Gson](https://github.com/google/gson), from coroutines in a scope that the provider owns. Add `com.squareup.okhttp3:okhttp` and `com.google.code.gson:gson` as dependencies of your app, or use the networking libraries your app already uses.

Now we will start implementing the CiscoDNA position provider. Create a class called `CiscoDNAPositionProvider` that implements the `MPPositionProvider` interface from the MapsIndoors SDK. Then create a constructor that takes a `Context`.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L18-L22)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var latestPosition: MPPositionResultInterface? = null
    // Live updates arrive on a background thread, so the listeners are kept in a thread-safe list
    private val positionUpdateListeners = CopyOnWriteArrayList<OnPositionUpdateListener>()
    // The provider's network requests run in this scope, and are cancelled when positioning stops
    private val scope = CoroutineScope(SupervisorJob() + Dispatchers.Default)
    private val httpClient = OkHttpClient()
    // If you do not have CiscoDNA set up through MapsIndoors you can write your tenantId here instead of using one from the config
    private val tenantId: String? = config.tenantId
}
```

We will start by implementing logic to each of the implemented methods from the `MPPositionProvider` interface. Some of these will be generic, while others will be specific to CiscoDNA. We also add a `notifyPositionUpdate` method that reports the latest position to the attached listeners.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L191-L201)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var latestPosition: MPPositionResultInterface? = null
    private val positionUpdateListeners = CopyOnWriteArrayList<OnPositionUpdateListener>()
    // ...

    override fun addOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.add(listener)
    }

    override fun removeOnPositionUpdateListener(listener: OnPositionUpdateListener) {
        positionUpdateListeners.remove(listener)
    }

    override fun getLatestPosition(): MPPositionResultInterface? = latestPosition

    private fun notifyPositionUpdate() {
        val position = latestPosition ?: return
        positionUpdateListeners.forEach { it.onPositionUpdate(position) }
    }
}
```

The CiscoDNA positioning requires three parameters:

1. The device’s IPv4 address (LAN)
2. The external IP address of the network in question (WAN)
3. The Tenant ID

Start by creating a method to retrieve the LAN address:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L136-L153)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    // ...

    private fun getLocalAddress(): String? = try {
        NetworkInterface.getNetworkInterfaces().asSequence()
            .flatMap { it.inetAddresses.asSequence() }
            .firstOrNull { !it.isLoopbackAddress && it is Inet4Address }
            ?.hostAddress
    } catch (e: SocketException) {
        Log.e("CiscoDNAPositionProvider", "Failed to resolve LAN address", e)
        null
    }

    // ...
}
```

Then create a `suspend` method to retrieve the WAN address (we recommend using a 3rd party service for this). The blocking request runs on the IO dispatcher:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L155-L173)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    // ...

    private suspend fun fetchExternalAddress(): String? = withContext(Dispatchers.IO) {
        val request = Request.Builder().url("https://ipinfo.io/ip").build()
        try {
            httpClient.newCall(request).execute().use { response ->
                if (response.isSuccessful) response.body?.string()?.trim() else null
            }
        } catch (e: IOException) {
            Log.e("CiscoDNAPositionProvider", "Failed to resolve WAN address", e)
            null
        }
    }

    // ...
}
```

If all of the three above mentioned strings can be acquired, you can ask our endpoint for a CiscoDNA Device ID string. A device ID is only available if there has been a recorded positioning for the device in the past 24 hours. We will implement this as a new method into our `CiscoDNAPositionProvider`.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L101-L134)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    // ...

    /**
     * Gathers the local and external IP addresses, and acquires a device ID from the Cisco DNA API.
     */
    private suspend fun updateAddressesAndId() {
        lan = getLocalAddress()
        wan = fetchExternalAddress()
        val tenantId = tenantId ?: return
        val lan = lan ?: return
        val wan = wan ?: return

        val url = "$MAPSINDOORS_CISCO_ENDPOINT$tenantId/api/ciscodna/devicelookup?clientIp=$lan&wanIp=$wan"
        val request = Request.Builder().url(url).build()
        withContext(Dispatchers.IO) {
            try {
                httpClient.newCall(request).execute().use { response ->
                    if (response.isSuccessful) {
                        val json = response.body?.string() ?: return@use
                        ciscoDeviceId = Gson().fromJson(json, JsonObject::class.java).get("deviceId")?.asString
                    } else {
                        Log.d("CiscoDNAPositionProvider", "Could not obtain deviceId from backend deviceID request! Code: ${response.code}")
                    }
                }
            } catch (e: IOException) {
                Log.e("CiscoDNAPositionProvider", "Device ID lookup failed", e)
            }
        }
    }

    // ...

    private companion object {
        const val MAPSINDOORS_CISCO_ENDPOINT = "https://ciscodna.mapsindoors.com/"
    }
}
```

Now you can make a method to start a subscription that we use when starting positioning to receive position updates. You use the SDKs `LiveDataManager` to create a subscription.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L52-L64)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    private var ciscoDNATopic: CiscoDNATopic? = null
    // ...

    private fun startSubscription() {
        val tenantId = tenantId ?: return
        val deviceId = ciscoDeviceId ?: return
        val topic = CiscoDNATopic(tenantId, deviceId)
        ciscoDNATopic = topic
        LiveDataManager.getInstance().setOnTopicSubscribedListener { subscribedTopic ->
            if (subscribedTopic == topic) {
                Log.i("CiscoDNA", "topic subscribed to successfully")
            }
        }
        LiveDataManager.getInstance().subscribeTopic(topic)
    }

    private fun unsubscribe() {
        ciscoDNATopic?.let { LiveDataManager.getInstance().unsubscribeTopic(it) }
    }

    // ...
}
```

To handle the subscription we just created, we need to register a callback in the constructor of the `MPPositionProvider` to handle the position results of the subscription:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L44-L49)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    init {
        LiveDataManager.getInstance().setOnReceivedLiveUpdateListener { _, liveUpdate ->
            if (liveUpdate.id == ciscoDeviceId) {
                latestPosition = liveUpdate.positionResult
                notifyPositionUpdate()
            }
        }
    }

    // ...
}
```

Implement the `startPositioning` and `stopPositioning` methods as well as `update` and `obtainInitialPosition` to get the initial position from CiscoDNA. Stopping positioning cancels any request still in flight:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L66-L103)

```kotlin
class CiscoDNAPositionProvider(private val context: Context, private val config: MPCiscoDNAConfig) : MPPositionProvider {
    // ...

    fun startPositioning() {
        scope.launch {
            update()
            startSubscription()
        }
    }

    fun stopPositioning() {
        unsubscribe()
        scope.coroutineContext.cancelChildren()
    }

    private suspend fun update() {
        updateAddressesAndId()
        if (!ciscoDeviceId.isNullOrEmpty() && latestPosition == null) {
            obtainInitialPosition()
        }
    }

    private suspend fun obtainInitialPosition() {
        val tenantId = tenantId ?: return
        val deviceId = ciscoDeviceId ?: return
        val request = Request.Builder().url("$MAPSINDOORS_CISCO_ENDPOINT$tenantId/api/ciscodna/$deviceId").build()
        val json = withContext(Dispatchers.IO) {
            try {
                httpClient.newCall(request).execute().use { response ->
                    // A 404 means that no position has been recorded for the device
                    if (response.isSuccessful) response.body?.string() else null
                }
            } catch (e: IOException) {
                Log.e("CiscoDNAPositionProvider", "Failed to obtain the initial position", e)
                null
            }
        } ?: return

        latestPosition = Gson().fromJson(json, CiscoDNAEntry::class.java)
        notifyPositionUpdate()
    }

    // ...
}
```

Our implemented positioning provider will be handled in an activity or fragment.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L40-L54)

```kotlin
class MyFragment : Fragment() {
    private var positionProvider: CiscoDNAPositionProvider? = null

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
        // ...
        val context = requireActivity()
        MapsIndoors.load(context.applicationContext, "myapikey") { _, _ ->
            // The Cisco DNA configuration is null if Cisco DNA is not set up for the Solution
            val config = MapsIndoors.solution?.ciscoDNAConfig ?: return@load
            positionProvider = CiscoDNAPositionProvider(context, config)
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

A full example implementation of the Cisco DNA position provider can be found here: [PositionProviders](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning)
