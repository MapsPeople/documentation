---
description: >-
  Now we have a simple map with a floor selector where you can search for
  locations. When finishing this step you'll be able to create a directions
  between two points and change the transportation mode.
---

# Getting Directions

### Get Directions Between Two Locations​ <a href="#get-directions-between-two-locations" id="get-directions-between-two-locations"></a>

After having created our list of search results, we have a good starting point for creating directions between two Locations. Since our search only supports a single search, we will query a random Location within the venue when requesting a route, and use that as the basis for our Origin. Then we'll create a route, navigate to a view of the navigation details, and show a route on the map from the Origin to the Destination.

Now we will create a method that can generate a route for us with a Location (picked from the search list). Start by adding a property for the `MPDirectionsService` to your MapsActivity.

{% tabs %}
{% tab title="Kotlin - Google Maps" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L26)

```kotlin
class MapsActivity : FragmentActivity(), OnMapReadyCallback {
    private var directionsService: MPDirectionsService? = null
    // ...

    override fun onMapReady(googleMap: GoogleMap) {
        // ...
    }
}
```
{% endtab %}

{% tab title="Kotlin - Mapbox" %}
[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapBox/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L24)

```kotlin
class MapsActivity : FragmentActivity() {
    private var directionsService: MPDirectionsService? = null
    // ...
}
```
{% endtab %}
{% endtabs %}

Create a method called `createRoute(mpLocation: MPLocation)` on your `MapsActivity`. Route results are delivered to an [`MPResultListener<MPRouteResult>`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-result-listener/on-result.html), which you set on the `MPDirectionsService` as a lambda.

Use this method to query the [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface), which generates a route between two coordinates. We will use this to query a route with our hardcoded `userLocation` and a point from a MPLocation.

To generate a route with the `MPLocation`, we start by creating an `onClickListener` on our search `ViewHolder` inside the `SearchItemAdapter`. In the method `onBindViewHolder` we will call our `createRoute` on the `MapsActivity` for our route to be generated.

[SearchItemAdapter.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt#L18-L32)

```kotlin
override fun onBindViewHolder(holder: ViewHolder, position: Int) {
    // ...
    holder.itemView.setOnClickListener {
        mapActivity?.createRoute(locations[position])
        // Clearing map to remove the location filter from our search result
        mapActivity?.mapControl?.clearFilter()
    }
    // ...
}
```

We start by implementing logic to our createRoute method to query a route through [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface) and set a route result listener on it. When we call the `createRoute` through our `onClickListener` we will receive the result in that listener, which passes the first route to our `onRouteResult` method.

When we receive a result on our listener, we render the route through the `MPDirectionsRenderer`.

We create global variables of the [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) and [`MPDirectionsService`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-service/index.html?query=class%20MPDirectionsService%20:%20MPDirectionsServiceInterface) and expose the [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) as a property to access it from _fragments_ later on.

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L182-L237)

```kotlin
fun createRoute(mpLocation: MPLocation) {
    // If MPDirectionsService has not been instantiated, create it here and listen for its route results.
    val service = directionsService ?: MPDirectionsService().also { service ->
        service.setRouteResultListener { result, error -> onRouteResult(result?.routes?.firstOrNull(), error) }
        service.setTravelMode(MPTravelMode.WALKING)
        directionsService = service
    }

    // Use the location's venue to query an origin point for the route, within the venue bounds.
    val venueBounds = mpLocation.venue?.let { MapsIndoors.venues?.getVenueByName(it) }?.bounds
    if (venueBounds == null) {
        // Open a dialog telling the user to try another location, as no venue is assigned to the location.
        AlertDialog.Builder(this)
            .setTitle("No venue assigned")
            .setMessage("Please try another location")
            .show()
        return
    }
    val filter = MPFilter { mapExtend = MPMapExtend(venueBounds) }
    MapsIndoors.getLocationsAsync(null, filter) { locations, _ ->
        val origin = locations?.firstOrNull()
        lifecycleScope.launch {
            if (origin != null) {
                // Queries the MPDirectionsService for a route from the origin to the point of the location.
                service.query(origin.point, mpLocation.point)
            } else {
                AlertDialog.Builder(this@MapsActivity)
                    .setTitle("No locations found within venue of location")
                    .setMessage("Please try another location")
                    .show()
            }
        }
    }
}

private fun onRouteResult(route: MPRoute?, error: MIError?) {
    if (route == null) {
        Log.e("MapsActivity", "No route found: ${error?.message}")
        return
    }
    // The result can arrive on a background thread, so update the map and the UI from the main thread
    lifecycleScope.launch {
        // Create the MPDirectionsRenderer if it has not been instantiated.
        val renderer = directionsRenderer ?: MPDirectionsRenderer(mapControl).also { directionsRenderer = it }
        // Set the route on the directions renderer
        renderer.setRoute(route)
        // Create a new instance of the navigation fragment and assign it to the bottom sheet
        addFragmentToBottomSheet(NavigationFragment.newInstance(route))
    }
}
```

See the full implementation of these methods here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L182-L237)

Now we will implement logic to our `NavigationFragment` that we can put into our BottomSheet and show the steps for each route, as well as the time and distance it takes to travel the route.

Here we'll use a `viewpager` to allow the user to switch between each step, as well as display a "close" button so we are able to remove the route and the bottom sheet from the activity.

We will start by exposing the [`MPdirectionsRenderer`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-directions-renderer/index.html?query=class%20MPDirectionsRenderer) that we store on `MapsActivity` as a property with a private setter:

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L178-L180)

```kotlin
var directionsRenderer: MPDirectionsRenderer? = null
    private set
```

Inside the `NavigationFragment` we will implement logic to navigate through Legs of our Route.

[NavigationFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/NavigationFragment.kt)

```kotlin
class NavigationFragment : Fragment() {
    private var route: MPRoute? = null
    private val mapsActivity: MapsActivity
        get() = requireActivity() as MapsActivity

    // ...
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        val routeCollectionAdapter = RouteCollectionAdapter(this)
        val viewPager: ViewPager2 = view.findViewById(R.id.view_pager)
        viewPager.adapter = routeCollectionAdapter
        viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
            override fun onPageSelected(position: Int) {
                super.onPageSelected(position)
                val renderer = mapsActivity.directionsRenderer ?: return
                // When a page is selected, call the renderer with the index
                renderer.selectLegIndex(position)
                // Update the floor on MapControl, as the floor might have changed for the leg
                mapsActivity.mapControl.selectFloor(renderer.getSelectedLegFloorIndex())
            }
        })

        val closeBtn = view.findViewById<ImageButton>(R.id.close_btn)
        val nextBtn = view.findViewById<ImageButton>(R.id.next_btn)
        val backBtn = view.findViewById<ImageButton>(R.id.back_btn)
        val distanceTxtView = view.findViewById<TextView>(R.id.distance_text_view)
        val infoTxtView = view.findViewById<TextView>(R.id.info_text_view)

        // Button for closing the bottom sheet. Clears the route through the directions renderer as well.
        closeBtn.setOnClickListener {
            mapsActivity.removeFragmentFromBottomSheet(this)
            mapsActivity.directionsRenderer?.clear()
        }

        // Next button for going through the legs of the route.
        nextBtn.setOnClickListener {
            viewPager.setCurrentItem(viewPager.currentItem + 1, true)
        }

        // Back button for going through the legs of the route.
        backBtn.setOnClickListener {
            viewPager.setCurrentItem(viewPager.currentItem - 1, true)
        }

        val route = route ?: return
        // Describing the distance in meters
        distanceTxtView.text = "Distance: ${route.distance} m"
        // Describing the time it takes for the route in minutes
        infoTxtView.text = "Time for route: ${TimeUnit.SECONDS.toMinutes(route.duration.toLong())} minutes"
    }

    inner class RouteCollectionAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
        override fun createFragment(position: Int): Fragment = RouteLegFragment.newInstance(route?.legs?.get(position))

        override fun getItemCount(): Int = route?.legs?.size ?: 0
    }

    companion object {
        fun newInstance(route: MPRoute): NavigationFragment =
            NavigationFragment().apply { this.route = route }
    }
}
```

See the full implementation of `NavigationFragment` and the accompanying adapter here: [NavigationFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/NavigationFragment.kt)

We will then create a simple textview to describe each step of the Route Leg in the `RouteLegFragment` for the `ViewPager`:

[RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/RouteLegFragment.kt)

```kotlin
class RouteLegFragment : Fragment() {
    private var routeLeg: MPRouteLeg? = null

    // ...

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // Assigning views
        val fromTxtView = view.findViewById<TextView>(R.id.from_text_view)
        // Describe what to do for each step of the leg
        fromTxtView.text = routeLeg?.steps.orEmpty()
            .mapIndexed { i, routeStep -> "Step ${i + 1}: ${routeStep.maneuver}" }
            .joinToString("\n")
    }

    companion object {
        fun newInstance(routeLeg: MPRouteLeg?): RouteLegFragment =
            RouteLegFragment().apply { this.routeLeg = routeLeg }
    }
}
```

See the full implementation of the fragment here: [RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/RouteLegFragment.kt)

### Change Transportation Mode​ <a href="#change-transportation-mode" id="change-transportation-mode"></a>

In MapsIndoors, the transportation mode is referred to as **travel mode**. There are four travel modes, **walking**, **bicycling**, **driving** and **transit** (public transportation). The travel modes generally applies for outdoor navigation. Indoor navigation calculations are based on **walking** travel mode.

To swap Travel Modes you set the Travel Mode before making a query for the route:

```kotlin
fun createRoute(mpLocation: MPLocation) {
    // If MPDirectionsService has not been instantiated, create it here and listen for its route results.
    val service = directionsService ?: MPDirectionsService().also { service ->
        service.setRouteResultListener { result, error -> onRouteResult(result?.routes?.firstOrNull(), error) }
        directionsService = service
    }
    service.setTravelMode(MPTravelMode.WALKING)
    // Queries the MPDirectionsService for a route from the hardcoded user location to the point of the location.
    service.query(userLocation, mpLocation.point)
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

