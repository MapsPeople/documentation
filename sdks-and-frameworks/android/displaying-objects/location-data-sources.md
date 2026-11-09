# Location Data Sources

In this tutorial we will show how you can build a custom Location Source, representing locations of robot vacuums. The robots locations will be served from a mocked list and displayed on a map.

We will start by creating our implementation of a location source.

Create the class `RobotVacuumLocationSource` that implements `MPLocationSource`:

Implement the methods from MPLocationSource and extend the constructor of the `RobotVacuumLocationSource` to accept a list of locations that will represent the Robot vacuums.

```kotlin
class RobotVacuumLocationSource(initialRobots: List<MPLocation>) : MPLocationSource {
    private val robots = initialRobots.toMutableList()
    private val observers = mutableListOf<MPLocationsObserver>()
    private var status = MPLocationSourceStatus.NOT_INITIALIZED

    override fun getLocations(): List<MPLocation> = robots

    override fun addLocationsObserver(observer: MPLocationsObserver?) {
        observer?.let { observers.add(it) }
    }

    override fun removeLocationsObserver(observer: MPLocationsObserver?) {
        observer?.let { observers.remove(it) }
    }

    private fun notifyUpdateLocations() {
        observers.forEach { it.onLocationsUpdated(robots, this) }
    }

    override fun getStatus(): MPLocationSourceStatus = status

    override fun getSourceId(): Int = 10101010

    override fun clearCache() {
        robots.clear()
        observers.clear()
    }

    override fun terminate() {
        robots.clear()
        observers.clear()
    }
}
```

Create a `Fragment` or `Activity` that contains a map with MapsIndoors loaded.

Add a `BASE_POSITION MPLatLng` that will be used to calculate a random location for the Robot Vacuums.

```kotlin
private val BASE_POSITION = MPLatLng(57.0582502, 9.9504788)
```

Then we need to add some variables:

```kotlin
private var baseDisplayRule: MPDisplayRule? = null
private val locations = mutableListOf<MPLocation>()
private var robotVacuumLocationSource: RobotVacuumLocationSource? = null
private var updateJob: Job? = null
```

Create the baseDisplayRule after `MapsIndoors` has loaded:

```kotlin
MapsIndoors.load(applicationContext, "YOUR_MAPSINDOORS_API_KEY") { _, error ->
    if (error == null) {
        baseDisplayRule = MapsIndoors.mainDisplayRule
        setupLocationSource()
    }
}
```

Create a method to set up the `RobotVacuumLocationSource` inside your fragment or activity:

```kotlin
private fun setupLocationSource() {
    if (locations.isEmpty()) {
        generateLocations()
    }
    val locationSource = RobotVacuumLocationSource(locations)
    robotVacuumLocationSource = locationSource
    MapsIndoors.addLocationSources(listOf(locationSource)) { _, error ->
        if (error != null) {
            Log.e("LocationSources", "Could not add the location source: ${error.message}")
        }
    }
    locationSource.setup()
    startUpdatingPositions()
}
```

As seen in the example above we add the `RobotVacuumLocationSource` through `MapsIndoors.addLocationSources` and call `RobotVacuumLocationSource.setup()`

Add the following methods to `RobotVacuumLocationSource`. `setup` sets the status of the source to available and notifies `MapsIndoors` that locations are updated, and `updateLocations` replaces the robots with their updated positions and notifies the observers:

```kotlin
fun setup() {
    setStatus(MPLocationSourceStatus.AVAILABLE)
    notifyUpdateLocations()
}

private fun setStatus(newStatus: MPLocationSourceStatus) {
    status = newStatus
    observers.forEach { it.onStatusChanged(newStatus, this) }
}

fun updateLocations(updatedLocations: List<MPLocation>) {
    robots.clear()
    robots.addAll(updatedLocations)
    notifyUpdateLocations()
}
```

In the `setupLocationSource` method we call `generateLocations` to populate the location list with new locations. Each robot gets a display rule whose icon color reflects its charge:

```kotlin
private fun generateLocations() {
    locations.clear()
    for (i in 0..15) {
        val robotName = "vacuum$i"
        val startPosition = getRandomPosition()
        val floorIndex = Random.nextInt(4) * 10
        val location = MPLocation.Builder(robotName)
            .setPosition(startPosition.lat, startPosition.lng)
            .setFloorIndex(floorIndex)
            .setName(robotName)
            .setBuilding("Stigsborgvej")
            .build()
        applyRobotDisplayRule(location, charge = Random.nextInt(1, 100))
        locations.add(location)
    }
}

private fun applyRobotDisplayRule(location: MPLocation, charge: Int) {
    val base = baseDisplayRule ?: return
    val robotDisplayRule = MPDisplayRule(location.name, WeakReference(base))
    robotDisplayRule.isVisible = true
    val tint = when {
        charge >= 60 -> Color.GREEN
        charge >= 30 -> Color.YELLOW
        else -> Color.RED
    }
    robotDisplayRule.setIcon(R.drawable.ic_baseline_robo_vacuum, tint)
    MapsIndoors.addDisplayRuleForLocation(location, robotDisplayRule)
}

private fun getRandomPosition(): MPLatLng {
    val lat = BASE_POSITION.lat + (-4 + Random.nextInt(20)) * 0.000005
    val lng = BASE_POSITION.lng + (-4 + Random.nextInt(20)) * 0.000010
    return MPLatLng(lat, lng)
}
```

Create the `startUpdatingPositions` method that launches a coroutine in `lifecycleScope`, which calls `updateLocations` every half second and stops when the lifecycle is destroyed:

```kotlin
private fun startUpdatingPositions() {
    updateJob?.cancel()
    updateJob = lifecycleScope.launch {
        delay(2_000)
        while (isActive) {
            updateLocations()
            delay(500)
        }
    }
}
```

Create a method that can stop the position updates at any time by cancelling the coroutine:

```kotlin
fun stopUpdatingPositions() {
    updateJob?.cancel()
    updateJob = null
}
```

Create a method called `updateLocations` that will update the position of the Locations:

```kotlin
fun updateLocations() {
    val updatedLocations = locations.map { location ->
        val newPosition = getRandomPosition()
        val updatedLocation = MPLocation.Builder(location)
            .setPosition(newPosition.lat, newPosition.lng)
            .build()
        applyRobotDisplayRule(updatedLocation, charge = Random.nextInt(1, 100))
        updatedLocation
    }
    robotVacuumLocationSource?.updateLocations(updatedLocations)
}
```

[See the samples in the locationsources folder](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/locationsources)
