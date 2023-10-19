# Using Cisco DNA Spaces

To get started with Cisco DNA positioning, the MapsIndoors SDK offers all the required building blocks without any external dependencies.

The Position Provider implementation exists at the customer application level, and needs to use the `MPPositionProvider` interface from the MapsIndoors SDK. The MapsIndoors SDK can then use the positioning results given by the Position Provider when setting the Position Provider with `MapsIndoors.setPositionProvider(MPPositionProvider)`.

#### Floor Mapping for Android[​](https://docs.mapsindoors.com/cisco-dna#floor-mapping-for-android) <a href="#floor-mapping-for-android" id="floor-mapping-for-android"></a>

The Position Provider should align with the MapsIndoors Floor index convention (floors are indexed as e.g 0, 10, 20, 30 corresponding to ground floor, 1st floor, 2nd floor, 3rd floor, with negative floors indices allowed as well to indicate Floors below ground, e.g. -10). It is therefore up to the Position Provider class to convert any given Floor indexing from the positioning source to that of MapsIndoors.

For a typical Position Provider, the mapping from the positioning's index needs to be mapped to the MapsIndoors Floor format.

The MapsIndoors backend is closely integrated with the CiscoDNA platform, so the MapsIndoors backend handles the floor mapping conversion for that integration. From an application perspective no Floor mapping implementation is required when integrating CiscoDNA positioning through the MapsIndoors platform.

**Fetch Attributes from Solution**[**​**](https://docs.mapsindoors.com/cisco-dna#fetch-attributes-from-solution)

You can choose to fetch the Position Provider information (`CMS` > `Solution Details` > `App Settings` > `Position Provider`) from the CMS as follows:

```java
Map<String, Map<String, Object>> providerConfig = MapsIndoors.getSolution().getPositionProviderConfig();
```

The outer keyset (`Map<String, Map<String, Object>>`) contains the name of the positioning provider, for example, `indooratlas3` for IndoorAtlas, or `ciscodna` when using Cisco DNA Spaces.

The inner keyset (`Map<String, Object>`) consist of various attribute fields for a given positioning provider, such as keys, floor mapping etc. These attribute fields will vary across different positioning providers, so refer to their own documentation for details.

#### Implementing Cisco DNA for Android[​](https://docs.mapsindoors.com/cisco-dna#implementing-cisco-dna-for-android) <a href="#implementing-cisco-dna-for-android" id="implementing-cisco-dna-for-android"></a>

This Guide requires you to already have an activity that shows a MapsIndoors Map as well as a Cisco DNA network with positioning active.

Now we will start implementing the CiscoDNA position provider. Create a class called `CiscoDNAPositionProvider` that implements the `MPPositionProvider` interface from the MapsIndoors SDK. Then create a constructor that takes a `Context`.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L18-L22)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var mLatestPosition: MPPositionResultInterface? = null
    private val positionUpdateListeners = ArrayList<OnPositionUpdateListener>()
    //If you do not have CiscoDNA set up through MapsIndoors you can write your tenantId here instead of using one from the config
    private val tenantId: String? = config.tenantId
}
```

We will start by implementing logic to each of the implemented methods from the `MPPositionProvider` interface. Some of these will be generic, while others will be specific to CiscoDNA.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L191-L201)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var mLatestPosition: MPPositionResultInterface? = null
    private val positionUpdateListeners = ArrayList<OnPositionUpdateListener>()
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

The CiscoDNA positioning requires three parameters:

1. The device’s IPv4 address (LAN)
2. The external IP address of the network in question (WAN)
3. The Tenant ID

Start by creating a method to retrieve the LAN address:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L136-L153)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    ...
    private fun getLocalAddress(): String? {
        try {
            val en = NetworkInterface.getNetworkInterfaces()
            while (en.hasMoreElements()) {
                val intf = en.nextElement()
                val enumIpAddr = intf.inetAddresses
                while (enumIpAddr.hasMoreElements()) {
                    val inetAddress = enumIpAddr.nextElement()
                    if (!inetAddress.isLoopbackAddress && inetAddress is Inet4Address) {
                        return inetAddress.getHostAddress()
                    }
                }
            }
        } catch (ex: SocketException) {
            Log.e(this.javaClass.simpleName, "Failed to resolve LAN address")
        }
        return null
    }
    ...
}
```

Then create a method to retrieve the WAN address (we recommend using a 3rd party service for this):

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L155-L173)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    ...
    private fun fetchExternalAddress(listener: MPReadyListener) {
        val httpClient = OkHttpClient()
        val request: Request = Request.Builder().url("https://ipinfo.io/ip").build()
        httpClient.newCall(request).enqueue(object : Callback {
            override fun onFailure(call: Call, e: IOException) {
                listener.onResult()
            }

            @Throws(IOException::class)
            override fun onResponse(call: Call, response: Response) {
                if (response.isSuccessful) {
                    val str = response.body!!.string()
                    wan = str
                    response.close()
                }
                listener.onResult()
            }
        })
    }
    ...
}
```

If all of the three above mentioned strings can be acquired, you can ask our endpoint for a CiscoDNA Device ID string. A device ID is only available if there has been a recorded positioning for the device in the past 24 hours. We will implement this as a new method into our `CiscoDNAPositionProvider`.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L101-L134)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var ciscoDeviceId: String? = null
    private var lan: String? = null
    private var wan: String? = null
    val MAPSINDOORS_CISCO_ENDPOINT = "https://ciscodna.mapsindoors.com/"
    ...
    /**
     * This method is responsible for gathering the local and external IP addresses
     * as well as acquiring a device ID from the Cisco DNA API.
     */
    private fun updateAddressesAndId(onComplete: MPReadyListener?) {
        lan = getLocalAddress()
        //mCiscoDeviceId = null;
        fetchExternalAddress {
            if (tenantId != null && lan != null && wan != null) {
                val url: String = "$MAPSINDOORS_CISCO_ENDPOINT$tenantId/api/ciscodna/devicelookup?clientIp=$lan&wanIp=$wan"
                val client = OkHttpClient()
                val request: Request = Request.Builder().url(url).build()
                try {
                    val response = client.newCall(request).execute()
                    if (response.isSuccessful) {
                        val gson = Gson()
                        val json = response.body!!.string()
                        val jsonObject =
                            gson.fromJson(json, JsonObject::class.java)
                        ciscoDeviceId = jsonObject["deviceId"].asString
                    } else {
                        Log.d(
                            "ciscodnaprovider",
                            "Could not obtain deviceId from backend deviceID request! Code: " + response.code
                        )
                    }
                    response.close()
                } catch (e: IOException) {
                    e.printStackTrace()
                }
            }
            onComplete?.onResult()
        }
    }
    ...
}
```

Now you can make a method to start a subscription that we use when starting positioning to receive position updates. You use the SDKs `LiveDataManager` to create a subscription.

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L52-L64)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    private var ciscoDNATopic: CiscoDNATopic? = null;
    ...
    private fun startSubscription() {
        ciscoDNATopic = CiscoDNATopic(tenantId!!, ciscoDeviceId!!)
        LiveDataManager.getInstance().setOnTopicSubscribedListener { topic: MPLiveTopic ->
            if (topic == ciscoDNATopic) {
                Log.i("CiscoDNA", "topic subscribed to succesfully")
            }
        }
        LiveDataManager.getInstance().subscribeTopic(ciscoDNATopic)
    }

    private fun unsubscribe() {
        LiveDataManager.getInstance().unsubscribeTopic(ciscoDNATopic)
    }
    ...
}
```

To handle the subscription we just created, we need to create some callbacks in the constructor of the `MPPositionProvider` to handle the position results and lifecycle of the subscription:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L44-L49)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    init {
        LiveDataManager.getInstance().setOnReceivedLiveUpdateListener { mpLiveTopic, liveUpdate ->
            if (liveUpdate.id == ciscoDeviceId) {
                mLatestPosition = liveUpdate.positionResult
                notifyPositionUpdate()
            }
        }
    }
    ...
}
```

Implement the `startPositoning` and `stopPositioning` method as well as a `update` and `obtainInitialPosition` to get the initial position from CiscoDNA:

[CiscoDNAPositionProvider.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/CiscoDNAPositionProvider.kt#L66-L103)

```kotlin
class CiscoDNAPositionProvider (private val context: Context, private val config: MPCiscoDNAConfig): MPPositionProvider {
    val MAPSINDOORS_CISCO_ENDPOINT = "https://ciscodna.mapsindoors.com/"
    private val tenantId: String? = config.tenantId
    private var ciscoDeviceId: String? = null
    ...

    fun startPositioning() {
        update { startSubscription() }
    }

    fun stopPositioning() {
        LiveDataManager.getInstance().unsubscribeTopic(ciscoDNATopic)
    }

    fun update(onreadyListener: MPReadyListener) {
        updateAddressesAndId {
            if (!ciscoDeviceId.isNullOrEmpty()) {
                if (mLatestPosition == null) {
                    obtainInitialPosition(onreadyListener)
                }else {
                    onreadyListener.onResult()
                }
            }
        }
    }

    private fun obtainInitialPosition(listener: MPReadyListener) {
        val url = "$MAPSINDOORS_CISCO_ENDPOINT$tenantId/api/ciscodna/$ciscoDeviceId"
        val request: Request = Request.Builder().url(url).build()
        if (ciscoDeviceId != null && tenantId != null) {
            val httpClient = OkHttpClient()
            httpClient.newCall(request).enqueue(object : Callback {
                override fun onFailure(call: Call, e: IOException) {
                    listener.onResult()
                }

                @Throws(IOException::class)
                override fun onResponse(call: Call, response: Response) {
                    if (response.code == HttpURLConnection.HTTP_NOT_FOUND) {
                        listener.onResult()
                        return
                    }
                    val json = response.body!!.string()
                    val positionResult: CiscoDNAEntry = Gson().fromJson(json, CiscoDNAEntry::class.java)
                    mLatestPosition = positionResult
                    notifyPositionUpdate()
                    listener.onResult()
                    response.close()
                }
            })
        }
    }
    ...
}
```

Our implemented positioning provider will be handled in an activity or fragment.

[PositioningFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning/PositioningFragment.kt#L40-L54)

```kotlin
class myFragment: Fragment(), OnMapReadyCallback {
    private var mPositionProvider: CiscoDNAPositionProvider? = null

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?,): View {
        ...
        MapsIndoors.load(requireActivity().applicationContext, "myapikey") {
            mPositionProvider = IndoorAtlasPositionProvider(requireActivity(), MapsIndoors.getSolution()!!.indoorAtlasConfig!!)
            // Attach the position provider to the SDK
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

A full example implementation of the Cisco DNA position provider can be found here: [PositionProviders](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/positioning)
