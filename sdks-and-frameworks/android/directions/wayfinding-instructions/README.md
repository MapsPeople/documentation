---
description: Android v4
---

# Wayfinding Instructions

This tutorial will show how to work with the route model returned from a directions service call. It will also show how you can utilize interactions between the route rendering on the map, and text-based instructions showed in another view.

This tutorial will be based off the **MapsIndoors Samples** example found here: [WayFinding](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding).

An example of the view XML file for the `WayfindingFragment` this guide will use can be found here: [Wayfinding view](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/res/layout/fragment\_wayfinding.xml).

First, create properties for `MPLocation` and `MPRoute` objects to use later in describing wayfinding. Also create a property for `MPDirectionsRenderer` so we can control rendering through changes in the `ViewPager`.

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L31-L32)

```kotlin
private var route: MPRoute? = null
private var location: MPLocation? = null
private var directionsRenderer: MPDirectionsRenderer? = null
```

Next step we will create the `Fragment` that will contain a short description of each Leg of the route inside the `ViewPager`

An example of the view XML file for the `RouteLegFragment`, that this guide will use, can be found here: [RouteLeg view](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/res/layout/fragment\_route\_leg.xml).

We will start by adding the code inside the `WayfindingFragment`.

First, start by changing the code inside `onCreateView`.

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L36-L57)

```kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
    _binding = FragmentWayfindingBinding.inflate(inflater, container, false)

    MapsIndoors.load(requireActivity().applicationContext, "gettingstarted", null)

    val routeLegAdapter = RouteCollectionAdapter(this)
    val viewPager = binding.stepViewPager
    viewPager.adapter = routeLegAdapter
    viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
        override fun onPageSelected(position: Int) {
            super.onPageSelected(position)
        }
    })

    val supportMapFragment = childFragmentManager.findFragmentById(R.id.map) as SupportMapFragment
    mapView = supportMapFragment.view
    supportMapFragment.getMapAsync(this)
    return binding.root
}
```

Next we will create `FragmentStateAdapter` that will be used on the `ViewPager` to contain `RouteLegFragments`

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L169-L197)

```kotlin
inner class RouteCollectionAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
    override fun getItemCount(): Int = route?.legs?.size ?: 0

    override fun createFragment(position: Int): Fragment {
        val legs = route?.legs.orEmpty()
        val leg = legs[position]
        if (position == legs.lastIndex) {
            return RouteLegFragment.newInstance("Walk to ${location?.name}", leg.distance.toInt(), leg.duration.toInt())
        }
        val nextLeg = legs[position + 1]
        val firstStep = leg.steps.first()
        val lastFirstStep = nextLeg.steps.first()
        val lastStep = nextLeg.steps.last()

        val firstBuilding = MapsIndoors.buildings?.getBuilding(firstStep.startPoint.latLng)
        val lastBuilding = MapsIndoors.buildings?.getBuilding(lastStep.startPoint.latLng)
        return when {
            firstBuilding != null && lastBuilding != null ->
                RouteLegFragment.newInstance(getStepName(lastFirstStep, lastStep), leg.distance.toInt(), leg.duration.toInt())
            firstBuilding != null ->
                RouteLegFragment.newInstance("Exit: ${firstBuilding.name}", leg.distance.toInt(), leg.duration.toInt())
            else ->
                RouteLegFragment.newInstance("Enter: ${lastBuilding?.name}", leg.distance.toInt(), leg.duration.toInt())
        }
    }
}
```

Let's create a method to textually describe each Leg. This method creates a string that takes the first and last step of the next leg to create a description for the user on what to do at the end of the currently shown leg. You will also create a method to get a list of the different highway types the route can give the user. These are found as enums through the `MPHighway` class in the MapsIndoors SDK.

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L120-L167)

```kotlin
fun getStepName(startStep: MPRouteStep, endStep: MPRouteStep): String {
    val startStepFloorName = startStep.startLocation?.floorName.orEmpty()
    val endStepFloorName = endStep.endLocation?.floorName.orEmpty()
    // Fall back to the floor index when a floor has no name
    val startLevel = startStepFloorName.ifEmpty { startStep.startPoint.floorIndex.toString() }
    val endLevel = endStepFloorName.ifEmpty { endStep.endPoint.floorIndex.toString() }

    val highway = getActionNames().firstOrNull { it == startStep.highway }
    if (highway != null) {
        val highwayName = if (highway == MPHighway.STEPS) "stairs" else highway
        return "Take $highwayName to level $endLevel"
    }
    if (startStepFloorName == endStepFloorName) {
        return "Walk to next step"
    }
    return "Level $startLevel to $endLevel"
}

private fun getActionNames(): List<String> = listOf(
    MPHighway.ELEVATOR,
    MPHighway.ESCALATOR,
    MPHighway.STEPS,
    MPHighway.TRAVELATOR,
    MPHighway.RAMP,
    MPHighway.WHEELCHAIRLIFT,
    MPHighway.WHEELCHAIRRAMP,
    MPHighway.LADDER,
)
```

Now, lets create the `RouteLegFragment` to give context for the Legs in the `WayfindingFragment`

[RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/RouteLegFragment.kt#L47-L55)

```kotlin
private var step: String? = null
private var duration: Int? = null
private var distance: Int? = null

companion object {
    fun newInstance(step: String, distance: Int?, duration: Int?) =
        RouteLegFragment().apply {
            this.step = step
            this.distance = distance
            this.duration = duration
        }
}
```

You must also update the `onViewCreated` method to use the new views added earlier in the tutorial.

[RouteLegFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/RouteLegFragment.kt#L29-L47)

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    binding.stepTextView.text = step

    if (Locale.getDefault().country == "US") {
        binding.distanceTextView.text = "${distance?.times(3.281)?.toInt()} feet"
    } else {
        binding.distanceTextView.text = "$distance m"
    }
    duration?.let {
        if (it < 60) {
            binding.durationTextView.text = "$it sec"
        } else {
            binding.durationTextView.text = "${TimeUnit.SECONDS.toMinutes(it.toLong())} min"
        }
    }
}
```

Now you have the revised UI for providing the user with a more explanatory route description when navigating. Now it needs to be rendered onto the map.

We will create a method named getRoute that queries a route between two location, we know exist on the current solution. When we receive the route we will assign the route to the fragment as well as calling `setRoute` on the `MPDirectionsRenderer` with the received Route. We will also notify the ViewPager that the route is updated with `notifyDataSetChanged`.

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L98-L118)

```kotlin
fun getRoute() {
    val directionsService = MPDirectionsService()
    val renderer = directionsRenderer ?: MPDirectionsRenderer(mapControl).also { directionsRenderer = it }

    directionsService.setRouteResultListener { result, error ->
        val mpRoute = result?.routes?.firstOrNull()
        if (error != null || mpRoute == null) return@setRouteResultListener
        // The result can arrive on a background thread, so update the map and the UI from the main thread
        viewLifecycleOwner.lifecycleScope.launch {
            route = mpRoute
            renderer.setRoute(mpRoute)
            binding.stepViewPager.adapter?.notifyDataSetChanged()
        }
    }
    val origin = MapsIndoors.getLocationById("5a07435a4e074edc9396b2ff")
    val destination = MapsIndoors.getLocationById("24ede0c9a5004a148bd01d96")
    location = destination
    if (origin != null && destination != null) {
        directionsService.query(origin.point, destination.point)
    }
}
```

To change the routing when swapping between tabs on the viewpager, use the callback that we added further up inside the `onCreateView` of `WayfindingFragment`.

[WayfindingFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding/WayfindingFragment.kt#L41-L50)

```kotlin
val routeLegAdapter = RouteCollectionAdapter(this)
val viewPager = binding.stepViewPager
viewPager.adapter = routeLegAdapter
viewPager.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
    override fun onPageSelected(position: Int) {
        super.onPageSelected(position)
        val renderer = directionsRenderer ?: return
        renderer.selectLegIndex(position)
        // The leg may be on another floor, so show that floor on the map
        mapControl.selectFloor(renderer.getSelectedLegFloorIndex())
    }
})
```

You should now have a Fragment with a map that has a route rendered on it. With descriptions of each Leg of the Route inside a ViewPager that will display the Leg related to the page that is viewed.

The full working example can be found here: [WayFinding](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/wayfinding).
