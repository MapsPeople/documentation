---
description: >-
  Now we have a simple map with a floor selector where you can search for
  locations. When finishing this step you'll be able to create a directions
  between two points and change the transportation mode.
---

# Getting Directions

### Get Directions Between Two Locations[​](https://docs.mapsindoors.com/getting-started/android/v4/directions#get-directions-between-two-locations) <a href="#get-directions-between-two-locations" id="get-directions-between-two-locations"></a>

After having created our list of search results, we have a good starting point for creating directions between two Locations. Since our search only supports a single search, we will query a random Location within the venue when requesting a route, and use that as the basis for our Origin. Then we'll create a route, navigate to a view of the navigation details, and show a route on the map from the Origin to the Destination.

Now we will create a method that can generate a route for us with a Location (picked from the search list). Start by implementing `OnRouteResultListener` to your MapsActivity.

{% tabs %}
{% tab title="Kotlin - Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L26)

```kotlin
class MapsActivity : FragmentActivity(), OnMapReadyCallback, OnRouteResultListener
```
{% endtab %}

{% tab title="Kotlin - Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L24)

```kotlin
class MapsActivity : FragmentActivity(), OnRouteResultListener
```
{% endtab %}
{% endtabs %}

Implement the [`onRouteResult` method](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-on-route-result-listener/on-route-result.html?query=abstract%20fun%20onRouteResult\(route:%20MPRoute,%20error:%20MIError\)) and create a method called `createRoute(MPLocation mpLocation)` on your `MapsActivity`.

Use this method to query the [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface), which generates a route between two coordinates. We will use this to query a route with our hardcoded `mUserLocation` and a point from a MPLocation.

To generate a route with the `MPLocation`, we start by creating an `onClickListener` on our search `ViewHolder` inside the `SearchItemAdapter`. In the method `onBindViewHolder` we will call our `createRoute` on the `MapsActivity` for our route to be generated.

[SearchItemAdapter.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt#L18-L32)

```kotlin
override fun onBindViewHolder(holder: ViewHolder, position: Int) {
    ...
    holder.itemView.setOnClickListener {
        mLocations[position]?.let { locations -> mMapActivity?.createRoute(locations) }
        //Clearing map to remove the location filter from our search result
        mMapActivity?.getMapControl()?.clearFilter()
    }
    ...
}
```

We start by implementing logic to our createRoute method to query a route through [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface) and assign the onRouteResultListener to the activity. When we call the `createRoute` through our `onClickListener` we will receive a result through our `onRouteResult` implementation.

When we receive a result on our listener, we render the route through the `MPDirectionsRenderer`.

We create global variables of the [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) and [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface) and create a getter to the [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) to access it from _fragments_ later on.

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L182-L237)

```kotlin
fun createRoute(mpLocation: MPLocation) {
    //If MPRoutingProvider has not been instantiated create it here and assign the results call back to the activity.
    if (mpRoutingProvider == null) {
        mpRoutingProvider = MPDirectionsService(this)
        mpRoutingProvider?.setRouteResultListener(this)
        mpRoutingProvider?.setTravelMode(MPTravelMode.WALKING)
    }

    //Use the locations venue to query an origin point for the route. Within the venue bounds.
    if (mpLocation.venue == null) {
        //Open dialog telling user to try another location, as no venue is assigned to the location.
        AlertDialog.Builder(this)
            .setTitle("No venue assigned")
            .setMessage("Please try another location")
            .show()
    } else {
        val venue = MapsIndoors.getVenues()?.getVenueByName(mpLocation.venue!!)
        MapsIndoors.getLocationsAsync(null, MPFilter.Builder().setMapExtend(MPMapExtend(venue!!.bounds!!)).build()) { list: List<MPLocation?>?, miError: MIError? ->
            if (!list.isNullOrEmpty()) {
                list.first()?.let { location ->
                    //Queries the MPRouting provider for a route with the hardcoded user location and the point from a location.
                    mpRoutingProvider?.query(location.point, mpLocation.point)
                }
            } else {
                AlertDialog.Builder(this)
                    .setTitle("No locations found within venue of location")
                    .setMessage("Please try another location")
                    .show()
            }
        }
    }
}

override fun onRouteResult(@Nullable route: Route?, @Nullable miError: MIError?) {
    ...
    //Create the MPDirectionsRenderer if it has not been instantiated.
    if (mpDirectionsRenderer == null) {
        mpDirectionsRenderer = MPDirectionsRenderer(mMapControl)
    }
    //Set the route on the Directions renderer
    mpDirectionsRenderer?.setRoute(route)
    //Create a new instance of the navigation fragment
    mNavigationFragment = NavigationFragment.newInstance(route, this)
    //Start a transaction and assign it to the BottomSheet
    addFragmentToBottomSheet(mNavigationFragment)
}
```

See the full implementation of these methods here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L182-L237)

Now we will implement logic to our `NavigationFragment` that we can put into our BottomSheet and show the steps for each route, as well as the time and distance it takes to travel the route.

Here we'll use a `viewpager` to allow the user to switch between each step, as well as display a "close" button so we are able to remove the route and the bottom sheet from the activity.

We will start by making a getter for our [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v4/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) that we store on `MapsActivity`:

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L178-L180)

```kotlin
fun getMpDirectionsRenderer(): MPDirectionsRenderer? {
    return mpDirectionsRenderer
}
```

Inside the `NavigationFragment` we will implement logic to navigate through Legs of our Route.

[NavigationFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/NavigationFragment.kt)

```kotlin
class NavigationFragment : Fragment() {
    private var mRoute: MPRoute? = null
    private var mMapsActivity: MapsActivity? = null

    ...
    override fun onViewCreated(view: View, @Nullable savedInstanceState: Bundle?) {
        val routeCollectionAdapter = RouteCollectionAdapter(this)
        val mViewPager: ViewPager2 = view.findViewById(R.id.view_pager)
        mViewPager.adapter = routeCollectionAdapter
        mViewPager.registerOnPageChangeCallback(object : OnPageChangeCallback() {
            override fun onPageSelected(position: Int) {
                super.onPageSelected(position)
                //When a page is selected call the renderer with the index
                mMapsActivity?.getMpDirectionsRenderer()?.selectLegIndex(position)
                //Update the floor on mapcontrol if the floor might have changed for the routing
                mMapsActivity?.getMpDirectionsRenderer()?.selectedLegFloorIndex?.let {floorIndex ->
                    mMapsActivity?.getMapControl()?.selectFloor(floorIndex)
                }
            }
        })

        ...

        //Button for closing the bottom sheet. Clears the route through directionsRenderer as well, and changes map padding.
        closeBtn.setOnClickListener {
            mMapsActivity!!.removeFragmentFromBottomSheet(this)
            mMapsActivity!!.getMpDirectionsRenderer()?.clear()
        }

        //Next button for going through the legs of the route.
        nextBtn.setOnClickListener {
            mViewPager.setCurrentItem(
                mViewPager.currentItem + 1,
                true
            )
        }

        //Back button for going through the legs of the route.
        backBtn.setOnClickListener {
            mViewPager.setCurrentItem(
                mViewPager.currentItem - 1,
                true
            )
        }

        //Describing the distance in meters
        distanceTxtView.text = "Distance: " + mRoute?.getDistance().toString() + " m"
        //Describing the time it takes for the route in minutes
        infoTxtView.text = "Time for route: " + mRoute?.duration?.toLong()?.let {duration ->
            TimeUnit.MINUTES.convert(duration, TimeUnit.SECONDS).toString()
        } + " minutes"
    }

    inner class RouteCollectionAdapter(fragment: Fragment?) :
        ...
    }

    companion object {
        fun newInstance(route: Route?, mapsActivity: MapsActivity?): NavigationFragment {
            val fragment = NavigationFragment()
            fragment.mRoute = route
            fragment.mMapsActivity = mapsActivity
            return fragment
        }
    }
}
```

See the full implementation of `NavigationFragment` and the accompanying adapter here: [NavigationFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/NavigationFragment.kt)

We will then create a simple textview to describe each step of the Route Leg in the `RouteLegFragment` for the `ViewPager`:

[RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/RouteLegFragment.kt)

```kotlin
class RouteLegFragment : Fragment() {
    private var mRouteLeg: MPRouteLeg? = null

    ...

    override fun onViewCreated(view: View, @Nullable savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        //Assigning views
        val fromTxtView = view.findViewById<TextView>(R.id.from_text_view)
        var stepsString = ""
        //A loop to write what to do for each step of the leg.
        for (i in mRouteLeg!!.steps.indices) {
            val routeStep = mRouteLeg!!.steps[i]
            stepsString += """
                Step ${i + 1}${routeStep.maneuver}
                """.trimIndent()
        }

        fromTxtView.text = stepsString
    }

    companion object {
        fun newInstance(routeLeg: MPRouteLeg?): RouteLegFragment {
            val fragment = RouteLegFragment()
            fragment.mRouteLeg = routeLeg
            return fragment
        }
    }
}
```

See the full implementation of the fragment here: [RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/RouteLegFragment.kt)

### Change Transportation Mode[​](https://docs.mapsindoors.com/getting-started/android/v4/directions#change-transportation-mode) <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

To swap Travel Modes you set the Travel Mode before making a query for the route:

```kotlin
fun createRoute(mpLocation: MPLocation) {
    //If MPDirectionsService has not been instantiated create it here and assign the results call back to the activity.
    if (mpDirectionsService == null) {
        mpDirectionsService = MPDirectionsService(this)
        mpDirectionsService?.setRouteResultListener(this)
    }
    mpDirectionsService?.setTravelMode(MPTravelMode.WALKING)
    //Queries the MPRouting provider for a route with the hardcoded user location and the point from a location.
    mpDirectionsService?.query(mUserLocation, mpLocation.point)
}
```

Expected result:

<figure><img src="../../../.gitbook/assets/android_directions_gif.gif" alt=""><figcaption></figcaption></figure>

Congratulations! You're at the end of your journey (for now), and you've accomplished a lot! 🎉

* You learned which prerequisites is needed to start building with MapsIndoors.
* You loaded a interactive map with MapsIndoors locations and added a floor selector for navigating between floors.
* You created a search experience to search for specific locations on the map.
* You added functionality for getting directions from one Location to another.

This concludes the "Getting Started" tutorial, but there's always more to discover. To get more inspiration on what to build next please visit our [showcase page](https://www.mapspeople.com/showcases) to see how other clients use MapsIndoors! For more documentation, please visit the rest of our Docs site!.