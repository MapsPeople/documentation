---
description: How to upgrade an Android app from MapsIndoors SDK 4.x to 5.0.0.
---

# Migrating from V4 to V5

Version 5 of the MapsIndoors Android SDK is written in Kotlin. Its public API now follows Kotlin conventions: there is a small set of generic listener types instead of one interface per callback, sealed types for events, geometry and errors, Kotlin DSL builders, and properties in place of getters and setters.

Version 5 has no compatibility layer. Every change below is a compile error, not a deprecation warning, so you can port an app by fixing each error in turn. This guide lists every change and its replacement. The sections near the top cover what nearly every app touches, and the reference tables at the end cover the rest.

The before-and-after examples are written against 4.22.x and 5.0.0.

{% hint style="info" %}
**Java apps are less affected than Kotlin apps.** A Kotlin property still compiles to `getX()` / `setX()`, so most changes that turn a getter into a property only affect Kotlin code. Each table says which language a change affects.
{% endhint %}

## Build Requirements

Version 5 raises the minimum toolchain. The AAR metadata enforces `compileSdk` and the Android Gradle Plugin version, so an older build fails with a clear error instead of crashing at runtime.

| Requirement | V4 (4.22.x) | V5 |
| --- | --- | --- |
| `minSdk` | 21 | **24** |
| `compileSdk` | 36 | **37** (enforced) |
| Android Gradle Plugin | 8.9.3 | **9.2.1 or newer** (enforced) |
| Kotlin (Kotlin apps) | not enforced | **2.2 or newer** |
| Java source / toolchain | 11 / 17 | 11 / 17 |
| kotlinx-coroutines | 1.6 | 1.10+ (exposed through the SDK's `api` dependencies) |

```kotlin
// app/build.gradle.kts
android {
    compileSdk = 37
    defaultConfig {
        minSdk = 24
    }
}
```

## Artifact Coordinates

The group and the artifact IDs are unchanged. Only the version changes:

```kotlin
dependencies {
    // Google Maps
    implementation("com.mapspeople.mapsindoors:googlemaps:5.0.0")

    // or Mapbox
    implementation("com.mapspeople.mapsindoors:mapbox-v11:5.0.0")
}
```

* `core`, `googlemaps`, `mapbox-v11` and `mapbox-flutter` are all published as before.
* **`mapbox-ndk27` is no longer published.** `mapbox-v11` now ships the 16 KB page-aligned Mapbox build itself. If you depended on `mapbox-ndk27`, switch to `mapbox-v11`.
* `core` no longer depends on `com.mapspeople:micommon`. It now pulls in the shared MapsIndoors Kotlin libraries (`mapsindoors-search`, `mapsindoors-directions`, `mapsindoors-log`) instead. If your app declared `micommon` itself, remove it.

## Initialization

`MapsIndoors.load` and `MapControl.create` keep their names and parameters. Only the listener types change: both now take an `MPResultListener<T>`, which reports `(result, error)`.

**V4**

```kotlin
MapsIndoors.load(applicationContext, "my-api-key") { error ->
    if (error == null) {
        // MapsIndoors is ready
    }
}

val mapConfig = MPMapConfig.Builder(activity, googleMap, getString(R.string.google_maps_key), mapView, true)
    .setShowFloorSelector(true)
    .build()

MapControl.create(mapConfig) { mapControl, error ->
    if (mapControl != null && error == null) {
        // MapControl is ready
    }
}
```

**V5**

```kotlin
MapsIndoors.load(applicationContext, "my-api-key") { _, error ->
    if (error == null) {
        // MapsIndoors is ready
    }
}

val mapConfig = MPMapConfig.Builder(activity, googleMap, getString(R.string.google_maps_key), mapView, true)
    .setShowFloorSelector(true)
    .build()

MapControl.create(mapConfig) { mapControl, error ->
    if (mapControl != null && error == null) {
        // MapControl is ready
    }
}
```

The load listener is now `MPResultListener<Unit>`, so its lambda takes two parameters, and the first one carries no value. From Java, the lambda becomes `(unit, error) -> { … }`.

`MPMapConfig.Builder` has the same constructors and setters for both Google Maps and Mapbox. The exceptions are `setMapLabelFont(Typeface, String, boolean)` and `setMapLabelTextSize(int)`, which are removed. See [Removed Deprecated Members](#removed-deprecated-members).

`MapsIndoors.addOnMapsIndoorsReadyListener` / `removeOnMapsIndoorsReadyListener` take an `MPResultListener<Unit>` as well.

## Listeners

Version 4 had a separate interface for almost every callback. Version 5 replaces most of them with three generic types and one sealed event hierarchy:

| Type | Shape | Used for |
| --- | --- | --- |
| `MPResultListener<T>` | `onResult(result: T?, error: MIError?)` | One-shot results: loading, searching, routing, geocoding, image loading |
| `MPStateListener<T>` | `onState(state: T)` | State changes that cannot be consumed: the current venue, the current building, venue status, dataset-cache status |
| `OnMPEvent<E : MPEvent>` | `onEvent(event: E): Boolean` | Events: clicks, selection, floor updates, camera events. Return `true` to consume the event and stop other listeners from seeing it, or `false` to let it propagate |
| `MPEvent` | sealed interface | The root of every event type. You can `when` over it exhaustively, but you cannot implement it |

All three listener types are `fun interface`s, so a lambda works from both Kotlin and Java.

**V4**

```kotlin
mapControl.setOnLocationSelectedListener { location ->
    showDetails(location)
    false
}

mapControl.setOnMapClickListener { latLng, locations ->
    false
}

mapControl.addOnFloorUpdateListener { building, floorIndex ->
    updateFloorLabel(floorIndex)
}

mapControl.setOnCurrentVenueChangedListener { venue ->
    updateVenueTitle(venue)
}
```

**V5**

```kotlin
mapControl.setOnLocationSelectedListener { event ->
    showDetails(event.location)
    false
}

mapControl.setOnMapClickListener { event ->
    // event.point, event.locations
    false
}

mapControl.addOnFloorUpdateListener { event ->
    updateFloorLabel(event.floorIndex)
    false
}

mapControl.setOnCurrentVenueChangedListener { venue ->
    updateVenueTitle(venue)
}
```

The event is a data class that carries what the old listener received as separate parameters. Listeners that returned `void` in V4, such as the floor-update and info-window listeners, now return a `Boolean`. Return `false` to keep the V4 behavior, where every listener is notified.

### MapControl listeners

| `MapControl` method | V4 listener | V5 listener |
| --- | --- | --- |
| `setOnLocationSelectedListener` | `OnLocationSelectedListener` | `OnMPEvent<MPLocationSelectedEvent>` — `event.location` |
| `setOnMapClickListener` | `MPOnMapClickListener` | `OnMPEvent<MPMapClickEvent>` — `event.point`, `event.locations` |
| `setOnMarkerClickListener` | `MPOnMarkerClickListener` | `OnMPEvent<MPMarkerClickEvent>` — `event.marker` |
| `setOnLocationClusterClickListener` | `OnLocationClusterClickListener` | `OnMPEvent<MPClusterClickEvent>` — `event.position`, `event.locations` |
| `setOnMarkerInfoWindowClickListener` | `MPOnInfoWindowClickListener` | `OnMPEvent<MPInfoWindowClickEvent>` — `event.marker` |
| `setOnMarkerInfoWindowCloseListener` | `MPOnInfoWindowClickListener` | `OnMPEvent<MPInfoWindowCloseEvent>` — `event.marker` |
| `addOnFloorUpdateListener` / `removeOnFloorUpdateListener` | `OnFloorUpdateListener` | `OnMPEvent<MPFloorUpdateEvent>` — `event.building`, `event.floorIndex` |
| `addOnCameraEventListener` / `removeOnCameraEventListener` | `MPCameraEventListener` | `OnMPEvent<MPCameraEvent>` — see [Camera Events](#camera-events) |
| `setOnCurrentBuildingChangedListener` | `OnBuildingFoundAtCameraTargetListener` | `MPStateListener<MPBuilding?>` |
| `setOnCurrentVenueChangedListener` | `OnVenueFoundAtCameraTargetListener` | `MPStateListener<MPVenue?>` |
| `setOnWillUpdateLocationsOnMap` | `OnWillUpdateLocationsOnMap` | **Removed**, with no replacement (it was deprecated in V4) |

### Removed listener interfaces

Every interface below is gone in V5. Use the type in the right-hand column instead.

| V4 interface | V5 replacement |
| --- | --- |
| `OnMapsIndoorsReadyListener` | `MPResultListener<Unit>` (`MapsIndoors.load`, `addOnMapsIndoorsReadyListener`) |
| `OnMapControlReadyListener` | `MPResultListener<MapControl>` (`MapControl.create`) |
| `OnLocationsReadyListener` | `MPResultListener<List<MPLocation>>` (`MapsIndoors.getLocationsAsync`) |
| `OnResultReadyListener` | `MPResultListener<Unit>` (`MapsIndoors.setLocationSources` / `addLocationSources` / `removeLocationSources`, `MPApiKeyValidator.checkAPIKeyValidity` / `checkAuthToken`) |
| `OnResultAndDataReadyListener<T>` | `MPResultListener<T>` (`MPBookingService`) |
| `OnSyncDataReadyListener` | `MPResultListener<Unit>` (`MapsIndoors.synchronizeContent`) |
| `OnLoadingDataReadyListener` | `MPResultListener<Unit>` |
| `OnGeoCodeResultReadyListener` | `MPResultListener<MPGeoCodeResult>` (`MapsIndoors.reverseGeoCode`) |
| `OnAuthDetailsReadyListener` | `MPResultListener<MPAuthDetails>` (`MapsIndoors.getAuthenticationDetails`) |
| `OnSingleImageLoadedListener` | `MPResultListener<Bitmap>` (`MPImageProviderInterface.loadImageAsync`) |
| `OnMultipleImagesLoadedListener` | `MPResultListener<Map<String, MPImageBatchItem>>` (`MPImageProviderInterface.loadImagesAsync`) |
| `OnRouteResultListener` | `MPResultListener<MPRouteResult>` (`MPDirectionsService.setRouteResultListener`) — see [Directions](#directions) |
| `OnDistanceMatrixResultListener` | `MPResultListener<MPDistanceMatrixResponse>` (`MPDirectionsService.getMatrix`) |
| `OnDirectionContainsResultListener` | Removed. Nothing in the SDK used it |
| `MPVenueStatusListener` | `MPStateListener<MPVenueStatus>` (`MapsIndoors.addOnVenueStatusChangedListener`) — see below |
| `MPDataSetCacheListener` | `MPStateListener<MPDataSetCacheStatus>` (`MPDataSetCacheManager.addOnDataSetStatusChangedListener`), or collect `MPDataSetCacheManager.dataSetStatus` |
| `MPDataSetCacheSyncListener` | `MPStateListener<MPDataSetSyncStatus>` (`MPDataSetCacheManager.addOnSyncStatusChangedListener`), or collect `MPDataSetCacheManager.syncStatus` |
| `MPDataSetCacheManagerSizeListener` | `MPStateListener<MPCacheSizeEvent>`, or collect the `Flow<MPCacheSizeEvent>` returned by `getSyncSizesForDataSetCaches(caches)` |
| `OnLocationSelectedListener` | `OnMPEvent<MPLocationSelectedEvent>` |
| `MPOnMapClickListener` | `OnMPEvent<MPMapClickEvent>` |
| `MPOnMarkerClickListener` | `OnMPEvent<MPMarkerClickEvent>` |
| `OnLocationClusterClickListener` | `OnMPEvent<MPClusterClickEvent>` |
| `MPOnInfoWindowClickListener` | `OnMPEvent<MPInfoWindowClickEvent>` and `OnMPEvent<MPInfoWindowCloseEvent>` |
| `OnFloorUpdateListener` | `OnMPEvent<MPFloorUpdateEvent>` |
| `OnBuildingFoundAtCameraTargetListener` | `MPStateListener<MPBuilding?>` |
| `OnVenueFoundAtCameraTargetListener` | `MPStateListener<MPVenue?>` |
| `OnWillUpdateLocationsOnMap` | Removed, with no replacement |
| `MPOnFeatureClickListener` | Renamed `IOnFeatureClickListener`. It is only used by map-provider implementations, and feature clicks reach apps as `MPLocationSelectedEvent` |
| `OnFloorSelectedListener` | Removed. Floor changes reach apps as `MPFloorUpdateEvent` |
| `MPPermissionsAndPSListener` | Removed. Nothing in the SDK used it |
| `OnStateChangedListener` | Removed. Nothing in the public API used it |
| `OnLanguageWillChangeListener` / `OnLanguageDidChangeListener` | Removed from the public API. Nothing in the public API used them |

These listener interfaces are **unchanged**: `MPReadyListener`, `MPSuccessListener`, `MPLocationsObserver`, `MPLocationSourceOnStatusChangedListener`, `OnPositionUpdateListener`, `OnLegSelectedListener` and `OnFloorSelectionChangedListener`. `MPCameraEventListener` still exists, but only map-provider implementations use it.

### Venue status

`MPVenueStatus` was an enum and is now a sealed class whose cases carry the venue ID. `LOADING` and `FAILED` no longer exist.

**V4**

```kotlin
MapsIndoors.addOnVenueStatusChangedListener { venueId, status ->
    if (status == MPVenueStatus.LOADED) { /* … */ }
}
```

**V5**

```kotlin
MapsIndoors.addOnVenueStatusChangedListener { status ->
    when (status) {
        is MPVenueStatus.Loaded -> { /* status.venueId */ }
        is MPVenueStatus.Unavailable -> { }
        is MPVenueStatus.NoVenue -> { }
    }
}
```

### Dataset cache

The dataset-cache listeners and the `MPDataSetManagerStatus` int constants are replaced by sealed status types.

**V4**

```java
MPDataSetCacheManager.getInstance().addMPDataSetCacheSyncListener((cache, status) -> {
    if (status == MPDataSetManagerStatus.SYNC_FINISHED) { /* … */ }
    else if (status == MPDataSetManagerStatus.SYNC_FAILED) { /* … */ }
});
```

**V5**

```java
MPDataSetCacheManager.getInstance().addOnSyncStatusChangedListener(status -> {
    MPDataSetCache cache = status.getDataSetCache();
    if (status instanceof MPDataSetSyncStatus.Finished) { /* … */ }
    else if (status instanceof MPDataSetSyncStatus.Failed) { /* … */ }
});
```

In Kotlin, you can collect `MPDataSetCacheManager.getInstance().syncStatus` instead of registering a listener. The size query, `getSyncSizesForDataSetCaches`, now reports exactly one `MPCacheSizeEvent.Done` at the end of the whole query. In V4 it reported once for each cache.

## Camera Events

`MPCameraEvent` has moved from `com.mapsindoors.core.models` to `com.mapsindoors.core`, and its constants are now in PascalCase. Update both the import and every constant you reference.

| V4 | V5 |
| --- | --- |
| `IDLE` | `Idle` |
| `ON_MOVE` | `OnMove` |
| `MOVE_STARTED_GESTURE` | `MoveStartedGesture` |
| `MOVE_STARTED_API_ANIMATION` | `MoveStartedApiAnimation` |
| `MOVE_STARTED_DEVELOPER_ANIMATION` | `MoveStartedDeveloperAnimation` |
| `MOVE_CANCELLED` | `MoveCancelled` |
| `FINISHED` | `Finished` |
| `CANCELLED` | `Cancelled` |

**V4**

```kotlin
import com.mapsindoors.core.models.MPCameraEvent

mapControl.addOnCameraEventListener { event ->
    if (event == MPCameraEvent.IDLE) onCameraSettled()
}
```

**V5**

```kotlin
import com.mapsindoors.core.MPCameraEvent

mapControl.addOnCameraEventListener { event ->
    if (event == MPCameraEvent.Idle) onCameraSettled()
    false
}
```

{% hint style="warning" %}
If your app converts camera events to strings, for example with `name`, `valueOf` or a lookup table in a Flutter or React Native bridge, the new constant names change those strings. Code like that compiles without errors, so search for it by hand.
{% endhint %}

### goTo

`MapControl.goTo(entity, maxZoom)` was deprecated in 4.22.0. It is still present in V5, but you should move to `MPCameraBehavior`, which also lets you control animation, padding and floor changes. The `onCameraEvent` listener is now an `OnMPEvent<MPCameraEvent>`.

**V4**

```kotlin
mapControl.goTo(location, 20.0)
```

**V5**

```kotlin
mapControl.goTo(location, MPCameraBehavior.Builder().setMaxZoom(20.0).build())

// With a completion listener
mapControl.goTo(location, MPCameraBehavior.DEFAULT) { event ->
    if (event == MPCameraEvent.Finished) { /* camera settled */ }
    false
}
```

From Java, `goTo(entity, null)` is ambiguous. Write `goTo(entity)` instead.

## Search

`MapsIndoors.getLocationsAsync` takes an `MPResultListener<List<MPLocation>>`. `MPQuery` and `MPFilter` can now be built with a Kotlin DSL. Their Java `Builder`s still work.

**V4**

```kotlin
val query = MPQuery.Builder()
    .setQuery("coffee")
    .build()

val filter = MPFilter.Builder()
    .setTake(10)
    .setFloorIndex(1)
    .build()

MapsIndoors.getLocationsAsync(query, filter) { locations, error ->
    if (locations != null) showResults(locations)
}
```

**V5**

```kotlin
val query = MPQuery {
    query = "coffee"
}

val filter = MPFilter {
    take = 10
    floorIndex = 1
}

MapsIndoors.getLocationsAsync(query, filter) { locations, error ->
    if (locations != null) showResults(locations)
}
```

In V5, `MPFilter` has no public constructor, so create one through `MPFilter.Builder()` or the DSL. `MapControl.setFilter` is unchanged.

## Directions

`MPDirectionsService.setRouteResultListener` now delivers an `MPRouteResult` instead of an `MPRoute`. Read the route from `result.routes`. The deprecated `MPDirectionsService(context)` constructor is removed, so use `MPDirectionsService()`.

**V4**

```kotlin
val directionsService = MPDirectionsService(context)
val directionsRenderer = MPDirectionsRenderer(mapControl)

directionsService.setRouteResultListener { route, error ->
    if (route != null) {
        directionsRenderer.setRoute(route)
    }
}
directionsService.query(origin, destination)
```

**V5**

```kotlin
val directionsService = MPDirectionsService()
val directionsRenderer = MPDirectionsRenderer(mapControl)

directionsService.setRouteResultListener { result, error ->
    val route = result?.routes?.firstOrNull()
    if (route != null) {
        directionsRenderer.setRoute(route)
    }
}
directionsService.query(origin, destination)
```

The `MPRouteResult` you receive always has `status` `"OK"`. Its `geocodedWaypoints` and `errorMessage` are not filled in from the routing response. Errors are reported through the listener's `error` parameter.

`MPDirectionsRenderer` keeps its constructor, `setRoute`, `selectLegIndex`, `nextLeg`, `previousLeg`, `clear` and `setOnLegSelectedListener`. Its two deprecated styling methods are removed:

**V4**

```kotlin
directionsRenderer.setPolylineColors(Color.BLUE, Color.LTGRAY)
directionsRenderer.setAnimatedPolyline(true, true, 6000)
```

**V5**

```kotlin
directionsRenderer.setOptions(
    MPDirectionsRendererOptions(
        animatedOverlayColor = Color.BLUE,   // was the foreground color
        strokeColor = Color.LTGRAY,          // was the background color
        animationRepeating = true,
        animationSpeed = 300.0,              // metres per second
        animationMinDuration = 1.2,          // seconds
    )
)
```

{% hint style="warning" %}
**The route animation timing has changed.** In V4, a renderer that was never given options animated over a fixed 6 seconds. In V5 the duration always comes from `animationSpeed` and `animationMinDuration`, which default to 300 m/s and 1.2 s. The animation now also respects the system's animator-duration setting for reduced motion. To turn the animation off, call `directionsRenderer.setConfig(MPDirectionsRendererConfig(animation = MPDirectionsRendererConfig.Animation(type = MPRouteAnimationType.NONE)))`.
{% endhint %}

## Display Rules

The `MPDisplayRule` setters are Kotlin properties in V5. From Java, nothing changes. From Kotlin, assign the property instead of calling the setter. Boolean properties are named `isX`.

**V4**

```kotlin
val rule = MapsIndoors.getMainDisplayRule()
rule?.setVisible(true)
rule?.setLabelStyleTextSize(14)
rule?.setPolygonFillColor("#3071d9")
rule?.setIconSize(32, 32)
```

**V5**

```kotlin
val rule = MapsIndoors.mainDisplayRule
rule?.isVisible = true
rule?.labelStyleTextSize = 14
rule?.polygonFillColor = "#3071d9"
rule?.setIconSize(32, 32)
```

* `setIcon(...)`, `setIconSize(width, height)` and the `@ColorInt` color overloads (`setPolygonFillColor(Int)`, `setLabelStyleTextColor(Int)` and so on) are still functions.
* `MapsIndoors.getDefaultDisplayRule()` and `getMainDisplayRule()` are now the `defaultDisplayRule` and `mainDisplayRule` properties in Kotlin. `getDisplayRule(location | name | solutionDisplayRule)` is unchanged.
* `MPDisplayRuleOptions` has a DSL: `MPDisplayRuleOptions { isVisible = true; labelStyleTextSize = 14 }`.

The `MapControl` label styling methods that were deprecated in V4 are removed. Style labels through display rules instead:

**V4**

```kotlin
mapControl.setMapLabelTextSize(14)
mapControl.setMapLabelFont(typeface, "#000000", true)
```

**V5**

```kotlin
mapControl.setMapLabelFont(typeface)
MapsIndoors.mainDisplayRule?.apply {
    labelStyleTextSize = 14
    setLabelStyleTextColor(Color.BLACK)
    setLabelStyleHaloColor(Color.WHITE)
}
```

## Positioning Provider

`MPPositionProvider` is now a Kotlin interface with the same three members, so a provider written in Java compiles unchanged. A provider written in Kotlin needs to change in two places:

* `MPPositionResultInterface` exposes nullable properties instead of `has…()` / `get…()` pairs. `hasFloor()`, `getFloorIndex()`, `setFloorIndex()`, `hasBearing()` and `hasAccuracy()` are removed. The floor index now lives on the result's `MPPoint`.
* The `MPPositionResult(point, accuracy, bearing, floorIndex)` and `MPPositionResult(point, accuracy, floorIndex)` constructors are removed. Put the floor index on the point instead.

**V4**

```kotlin
class MyPositionProvider : MPPositionProvider {
    private val listeners = mutableListOf<OnPositionUpdateListener>()
    private var latest: MPPositionResultInterface? = null

    override fun addOnPositionUpdateListener(listener: OnPositionUpdateListener) { listeners += listener }
    override fun removeOnPositionUpdateListener(listener: OnPositionUpdateListener) { listeners -= listener }
    override fun getLatestPosition(): MPPositionResultInterface? = latest

    fun onFix(lat: Double, lng: Double, floorIndex: Int, accuracy: Float, bearing: Float) {
        val result = MPPositionResult(MPPoint(lat, lng), accuracy, bearing, floorIndex)
        result.provider = this
        latest = result
        listeners.forEach { it.onPositionUpdate(result) }
    }
}

// Reading a position
if (position.hasFloor()) useFloor(position.floorIndex)
```

**V5**

```kotlin
class MyPositionProvider : MPPositionProvider {
    private val listeners = mutableListOf<OnPositionUpdateListener>()
    private var latest: MPPositionResultInterface? = null

    override fun addOnPositionUpdateListener(listener: OnPositionUpdateListener) { listeners += listener }
    override fun removeOnPositionUpdateListener(listener: OnPositionUpdateListener) { listeners -= listener }
    override fun getLatestPosition(): MPPositionResultInterface? = latest

    fun onFix(lat: Double, lng: Double, floorIndex: Int, accuracy: Float, bearing: Float) {
        val result = MPPositionResult(MPPoint(lat, lng, floorIndex.toDouble()), accuracy, bearing)
        result.provider = this
        latest = result
        listeners.forEach { it.onPositionUpdate(result) }
    }
}

// Reading a position
position.point?.takeIf { it.floorIndex != MPFloor.NO_FLOOR_INDEX }?.let { useFloor(it.floorIndex) }
```

Register the provider as before. In Kotlin, `MapsIndoors.setPositionProvider(provider)` becomes `MapsIndoors.positionProvider = provider`. `MapControl.showUserPosition(true)` is unchanged.

## Builders and DSL

In Kotlin, several option types now have a DSL function with the same name as the type. Java keeps the `Builder`.

| Type | Kotlin DSL | Java |
| --- | --- | --- |
| `MPQuery` | `MPQuery { query = "…" }` | `new MPQuery.Builder()…build()` |
| `MPFilter` | `MPFilter { take = 10 }` | `new MPFilter.Builder()…build()` |
| `MPMarkerOptions` | `MPMarkerOptions { setPosition(latLng) }` | `MPMarkerOptions.builder()…build()` |
| `MPTileOverlayOptions` | `MPTileOverlayOptions { … }` | `MPTileOverlayOptions.builder(tileProvider)…build()` |
| `MPCameraPosition` | `MPCameraPosition { … }` | `new MPCameraPosition.Builder()…build()` |
| `MPLatLngBounds` | `MPLatLngBounds { … }` | `new MPLatLngBounds.Builder()…build()` |
| `MPMapStyleOptions` | `MPMapStyleOptions { … }` | Builder |
| `MPDisplayRuleOptions` | `MPDisplayRuleOptions { … }` | `new MPDisplayRuleOptions()` and its setters |
| `MPNetworkOptions` | `MPNetworkOptions { … }` | Builder |
| `MPBooking`, `MPBookableQuery`, `MPBookingsQuery`, `MPBookingAuthConfig` | `MPBooking { … }` and so on | Builder |
| `MPContextualInfoSettings` | `MPContextualInfoSettings { … }` | Builder |

`MPMarkerOptions` and `MPTileOverlayOptions` are now **immutable**. In V4 you created them and then called fluent setters on the object. In V5 you set everything on the builder. `MPTileOverlayOptions` requires its tile provider when it is built.

**V4**

```kotlin
val options = MPMarkerOptions()
    .setPosition(latLng)
    .setTitle("Entrance")
```

**V5**

```kotlin
val options = MPMarkerOptions {
    setPosition(latLng)
    setTitle("Entrance")
}
```

`MPCameraBehavior` and `MPDirectionsRendererOptions` have no DSL and are unchanged from 4.22.

## Getters Become Properties

Most public classes that were Java in V4 are Kotlin in V5, including `MapsIndoors`, `MPLocation`, `MPVenue`, `MPBuilding`, `MPFloor`, `MPPoint`, `MPLatLng`, `MPQuery`, `MPFilter`, `MPDisplayRule`, `MPSolution`, `MPCategory`, `MPUserRole`, `MIError` and `MPDataSetCacheManager`.

* **Java:** getters and setters keep their names (`getName()`, `setPositionProvider(…)`). Nothing changes.
* **Kotlin:** code that already used property syntax (`location.name`) compiles unchanged. Code that called the getter explicitly (`location.getName()`) must drop the `get` and use the property.

```kotlin
// V4 (Kotlin)
val venues = MapsIndoors.getVenues()
val ready = MapsIndoors.isReady()
MapsIndoors.setOfflineMode(true)

// V5 (Kotlin)
val venues = MapsIndoors.venues
val ready = MapsIndoors.isReady
MapsIndoors.isOfflineMode = true
```

A few types also became immutable or lost public fields, which affects Java as well:

* **`MIError`** is a data class. Its `code`, `message`, `status` and `tag` were public mutable fields in V4. In Java, read them with `getCode()`, `getMessage()` and so on. They can no longer be assigned.
* **`MPLatLng`** is immutable. `setLat` / `setLng` and the copy constructor are removed. In Kotlin, use `latLng.copy(lat = …)`. In Java, create a new `MPLatLng(lat, lng)`.
* **`MPVisibleRegion`** fields (`farLeft`, `nearRight`, …) are properties. In Java, read them with `getFarLeft()` and so on.
* **`MPMapStyle`** is immutable. `setFolder` and `setDisplayName` are removed.
* **`MPRouteProperty.value`** is read-only. `setValue` is removed.

## Sealed Types

Several public hierarchies are sealed in V5. You can `when` over them exhaustively in Kotlin, but you can no longer subclass them.

**Geometry.** `MPGeometry` is a sealed interface. Its only implementations are `MPPoint`, `MPPolygonGeometry` and `MPMultiPolygonGeometry`. In V4 it was an abstract class.

```kotlin
when (val geometry = location.geometry) {
    is MPPoint -> { }
    is MPPolygonGeometry -> { }
    is MPMultiPolygonGeometry -> { }
    null -> { }
}
```

Java code can keep using `instanceof` or `getIType()`.

**Errors.** `MapsIndoorsException` is a sealed class with the cases `NotInitialized`, `InvalidContext`, `InvalidState`, `MissingData`, `Routing` (carries an `MIError`) and `Unknown`. `MPBookingService`'s new `suspend` functions throw `MPBookingException`, which carries the `MIError`. `MIError` itself is described under [Getters Become Properties](#getters-become-properties).

**Events.** `MPEvent` and its families (`MPClickEvent`, `MPSelectionEvent`, `MPPositionEvent`) are sealed. See [Listeners](#listeners).

**Booking.** `MPBookingService` now has `suspend` versions of `getBookableLocations`, `getLocationsConfiguredForBooking`, `getBookingsUsingQuery`, `performBooking` and `cancelBooking`. The callback versions remain, and they take an `MPResultListener<T>`.

## Subclassing SDK Types

A few classes that apps sometimes extend are now closed or have a different shape:

* **`MPDefaultInfoWindow`** is `final`. To customize the info window, implement `IInfoWindowAdapter` and pass it with `MPMapConfig.Builder.setInfoWindowAdapter`.
* **`MPMapBehavior`** can no longer be created with a no-argument constructor, and its getters are `final`. Use `MPSelectionBehavior`, `MPHighlightBehavior` or `MPFilterBehavior` and their builders.
* **`MPIMapConfig` and `MPIMapConfig.Builder`** (only relevant if you write your own map provider). Their protected fields are now properties. A subclass that declares a member named `mMap`, `mContext`, `mMapView`, `mapsIndoorsTransitionLevel`, `hideProviderLogo` or `hiddenFeatures` no longer compiles (`'mMapView' hides member of supertype`), so rename or delete that member. `getContext()`, `getMapProvider()` and `getMapView()` now throw `IllegalStateException` instead of returning `null` on a config that was not created through a `Builder`.
* **`MPFloorSelectorInterface`**. Implementations written in Java compile unchanged. In Kotlin, `getView()` and `isAutoFloorChangeEnabled()` are now properties:

```kotlin
// V4
override fun getView(): View = container
override fun isAutoFloorChangeEnabled(): Boolean = true

// V5
override val view: View? get() = container
override val isAutoFloorChangeEnabled: Boolean get() = true
```

* **`MPImageProviderInterface`** (custom image providers). You now implement the `suspend` functions `loadImage(url)` and `loadImages(urls)`. `loadImageAsync` and `loadImagesAsync` become default methods built on top of them. `loadImagesAsync` delivers a `Map`, not a `HashMap`.
* **`MPDirectionsServiceExternalInterface` and `MPDistanceMatrixServiceInterface`** (custom map providers). You now implement `suspend route(...)` and `suspend matrix(...)`, and the callback methods become default methods. Because the members are `suspend`, these interfaces can only be implemented in Kotlin.
* **`MPClusterIconAdapter`** is unchanged. In Kotlin, it can now be written as a lambda.

## Internal APIs

{% hint style="warning" %}
**This section is not final.** The SDK work that moves internal classes into `com.mapsindoors.core.internal` and stops R8 keeping them for consumers has not shipped yet. When it does, this section will list the classes that move and how to opt in to the few that stay reachable.
{% endhint %}

In V5 so far, the following types that apps could reach in V4 are no longer public:

* `MPNodeData` is internal. Nothing public returned or accepted it.
* `MPDistanceMatrixService` is internal. Use `MPDirectionsService.getMatrix`, which is unchanged apart from its listener type.
* `MPDataSetCache.getFileCache()` is removed.

The following utility classes are still public in V5 but are intended for the SDK's own use. Avoid depending on them, because they are planned to move behind an opt-in annotation: `MPDebugLog`, `MPJsonParser`, `MPUriTemplate`, `MPFastSphericalUtils` and `MPRouteGeometry`.

## Removed Deprecated Members

Members that were deprecated in V4 are removed in V5:

| Removed | Replacement |
| --- | --- |
| `MapControl.setMapLabelTextSize(int)` | `MPDisplayRule.labelStyleTextSize` (`setLabelStyleTextSize` from Java). In V4 this method did nothing |
| `MapControl.setMapLabelFont(Typeface, String, boolean)` | `MapControl.setMapLabelFont(Typeface)`, plus `MPDisplayRule.setLabelStyleTextColor` / `setLabelStyleHaloColor`. In V4 the color and halo arguments were ignored |
| `MPIMapConfig.Builder.setMapLabelFont(Typeface, String, boolean)` / `setMapLabelTextSize(int)` | `setMapFont(Typeface)` on the builder, and display rules for color, halo and size |
| `MPIMapConfig.getFontColor()` / `getShowFontHalo()` | None. Nothing ever set these values |
| `MapsIndoors.isAPIKeyValid()` | None. It always returned `true`. To validate a key, use `MPApiKeyValidator.checkAPIKeyValidity` |
| `MPDirectionsRenderer.setPolylineColors(int, int)` | `MPDirectionsRendererOptions(animatedOverlayColor = …, strokeColor = …)` |
| `MPDirectionsRenderer.setAnimatedPolyline(boolean, boolean, int)` | `MPDirectionsRendererOptions(animationRepeating = …, animationSpeed = …, animationMinDuration = …)`. The animation timing has changed, see [Directions](#directions) |
| `MapControl.setOnWillUpdateLocationsOnMap` | None |
| `MPDirectionsService(Context)` | `MPDirectionsService()` |

`MapControl.goTo(entity, maxZoom)` is still deprecated but not yet removed. See [goTo](#goto).

## Live Data

The `com.mapsindoors.livedata` package is unchanged in V5. It is still Java, and its listeners (`OnLiveLocationUpdateListener`, `OnReceivedLiveUpdateListener` and the others) keep their V4 shapes. `MapControl.enableLiveData(domainType, listener)` is also unchanged. The package was not part of the Kotlin migration, because its future is still being decided. Expect changes to it in a later release.

## Behavior Changes

These changes do not break compilation, but they can change what your app observes:

* **Route animation** is always speed-based and respects reduced-motion settings. See [Directions](#directions).
* **`MPRouteResult`** from the routing callback always has status `"OK"`, and `geocodedWaypoints` / `errorMessage` are not filled in.
* **Dataset-cache syncs** always report exactly one final status (`Finished` or `Failed`). In V4, some failures reported nothing, which could leave `MapsIndoors.load` waiting forever.
* **`getSyncSizesForDataSetCaches`** reports `MPCacheSizeEvent.Done` once for the whole query, not once for each cache.
* **`MPImageProviderInterface.loadImageAsync`** reports an `MIError` when an image fails to load. In V4 the error was `null`. An image that downloads but cannot be decoded reports `IMAGEPROVIDER_LOADER_NO_RESULT_ERROR` (4001) instead of `UNKNOWN_ERROR` (20).
* **Cluster IDs** have a new format. The member IDs are separated by `_`, so `MPCluster--1-1-1` becomes `MPCluster--1_-1_-1`. Apps that parse cluster IDs need updating.
* **Routes that cross a venue boundary** use the active language for the indoor part. In V4 that part was always in English.
* **Offline routing** applies `addExcludeWayType`. V4 ignored it offline.
* **A graph payload with a `null` where a value is required** fails to load with `PARSE_ERROR`. In V4 it loaded and then caused a crash during offline routing.

## Reference: All Member-Level Changes

The tables below are generated from the SDK team's register of breaking changes, so they include every change listed there, grouped by area. *Who must change* says whether Java code, Kotlin code, or both need updating.

### Route results

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPRouteResult.getGeocoded_waypoints()` | removed — read the `geocodedWaypoints` property (`getGeocodedWaypoints()` from Java) | Java + Kotlin |
| `MPRouteResult.getStatus()` / `getRoutes()` / `setRoutes()` / `getErrorMessage()` | `status`, `routes`, `errorMessage` properties; the JVM accessors keep their names | Kotlin only |
| `MPRouteResult` public JVM fields | fields are private; use the accessors | Java field access |
| `MPRouteProperty.value` | `val value: Float` — `setValue(float)` is gone | Java + Kotlin |

### Map and value types

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPVisibleRegion.farLeft` / `farRight` / `nearLeft` / `nearRight` / `latLngBounds` | properties — use `getFarLeft()` etc. from Java | Java field access |
| `MPMarkerOptions` / `MPTileOverlayOptions` setters | build through `Type.builder()…build()` (Java) or the `Type { }` DSL (Kotlin); the options themselves are immutable | Java + Kotlin |
| `MPTileOverlayOptions` construction | `tileProvider` is required at construction | Java + Kotlin |
| `MPLatLng(MPLatLng)` | removed — use `copy()` | Kotlin (Java: construct from `lat`/`lng`) |
| `MPLatLng.setLat(float)` / `setLng(float)` | removed — the type is immutable | Java + Kotlin |
| `MPMapStyle.setFolder(...)` / `setDisplayName(...)` | removed — the type is immutable | Java + Kotlin |
| `MPGeoCodeResult` lists | immutable lists, populated at construction | callers that mutated them |

### Offline dataset cache

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPDataSetCacheListener` | removed — collect `MPDataSetCacheManager.dataSetStatus`, or register an `MPStateListener<MPDataSetCacheStatus>` with `addOnDataSetStatusChangedListener` | Java + Kotlin |
| `MPDataSetCacheSyncListener` | removed — collect `MPDataSetCacheManager.syncStatus`, or register an `MPStateListener<MPDataSetSyncStatus>` with `addOnSyncStatusChangedListener` | Java + Kotlin |
| `MPDataSetCacheManagerSizeListener` | removed — `getSyncSizesForDataSetCaches(caches)` now returns a cold `Flow<MPCacheSizeEvent>`; the callback overload takes an `MPStateListener<MPCacheSizeEvent>` and returns a `Job`, and always ends on `MPCacheSizeEvent.Done` | Java + Kotlin |
| `MPDataSetCacheManager.add/removeMPDataSetCacheListener` / `…SyncListener` | `addOnDataSetStatusChangedListener` / `removeOnDataSetStatusChangedListener` / `addOnSyncStatusChangedListener` / `removeOnSyncStatusChangedListener` | Java + Kotlin |
| `MPDataSetManagerStatus` | removed — `MPDataSetSyncStatus`, `MPDataSetCacheStatus` and the internal `MPDataSetItemStatus` replace them | Java + Kotlin |
| `MPDataSetCacheManager.getSyncSizesForDataSetCaches` | emits exactly one `MPCacheSizeEvent.Done`, after sizing the caches in order | Java + Kotlin |
| `MPDataSetCacheManager.reloadFromPersistenceFile()` | `suspend` — it reads the datasets file, so it runs on the IO dispatcher under the persistence `Mutex` | Kotlin (was not callable from Java without a coroutine anyway) |
| `MPDataSetCacheManager.synchronizeDataSets(…)` | returns the `Job` running the syncs; still fire-and-forget | Kotlin only (Java ignores the result) |
| `MPDataSetCache.getFileCache()` | `internal val fileCache` — `FileCache` is itself `internal`, so the public accessor could not survive the conversion | Java + Kotlin |
| `MPDataSetCache.getScope()` / `setScope(…)` | `var scope` — `getScope()`/`setScope()` from Java are unchanged | Kotlin only |
| `MPDataSetCacheItem.getTimestamp()` / `getSyncSize()` / `getCacheSize()` | `timestamp` / `syncSize` / `cacheSize` properties; the JVM accessors keep their names | Kotlin only |
| `MPDataSetCache.getVenueIds()` | an immutable `List` copy | callers that mutated the returned list |

### Image loading

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPImageProviderInterface.loadImageAsync` | `fun loadImageAsync(imageUrl: String, listener: MPResultListener<Bitmap>)` with a **default** body over `loadImage`; still callable, no longer implementable-only | Java + Kotlin implementors |
| `MPImageProviderInterface.loadImagesAsync` | `(List<String>, MPResultListener<Map<String, MPImageBatchItem>>)` with a default body over `loadImages`; the payload is `Map`, not `HashMap` | Java + Kotlin |
| `MPImageProviderInterface.loadImage` | **new**, abstract: `suspend fun loadImage(imageUrl: String): MPImageBatchItem` | Java + Kotlin implementors |
| `MPImageProviderInterface.loadImages` | **new**, abstract: `suspend fun loadImages(imageUrlList: List<String>): Map<String, MPImageBatchItem>` | Java + Kotlin implementors |
| `MPImageProviderInterface.loadImageAsync` failure payload | delivers `(null, MIError)` carrying the failing item's code | Java + Kotlin callers |
| `MPImageBatchItem` | `data class MPImageBatchItem(bitmap, httpStatus, tag = null)`; the JVM accessors keep their names, and `@JvmOverloads` keeps both constructors | Kotlin callers (property syntax); Java unaffected |
| `MPImageBatchItem.httpStatus` for an undecodable body | `MIError.IMAGEPROVIDER_LOADER_NO_RESULT_ERROR` (4001) | Java + Kotlin |

### Tiles

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPTileProvider.getTileSetVersion()` / `getTilesUrlTemplate()` / `getFloorIndex()` / `getMapStyle()` / `getLocalTilesPath()` | `tileSetVersion` / `tilesUrlTemplate` / `floorIndex` / `mapStyle` / `localTilesPath` properties; the JVM accessors keep their names | Kotlin only |
| `MPTileProvider.getTile(int, int, int)` | `MPTile?` — still synchronous, still nullable | Kotlin only (the platform type becomes an explicit nullable) |

### Clustering

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPClusterIconAdapter.getImage(List<MPLocation>)` | `fun getImage(locations: List<MPLocation>): Bitmap` — the annotations become Kotlin nullability | Kotlin implementors (the types are now enforced) |

### Camera events and removed listeners

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPCameraEvent` constants | `Idle`, `OnMove`, `MoveStartedGesture`, `MoveStartedApiAnimation`, `MoveStartedDeveloperAnimation`, `MoveCancelled`, `Finished`, `Cancelled` | Java + Kotlin |
| `MapControl.setOnWillUpdateLocationsOnMap` + `OnWillUpdateLocationsOnMap` | **both removed**, with no replacement. The method was already deprecated in v4. | Java + Kotlin |
| `OnRouteResultListener` | removed — `MPDirectionsService.setRouteResultListener` takes an `MPResultListener<MPRouteResult>` | Java + Kotlin |
| `OnDirectionContainsResultListener` | removed — nothing in the SDK used it | Java + Kotlin |

### Directions and distance-matrix services

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPDistanceMatrixServiceInterface.getMatrix(..., listener)` | `default`, bridging the new `suspend matrix(...)` | an adapter, which must now implement `matrix` instead. A caller is unaffected. |
| `MPDirectionsServiceExternalInterface.query(..., listener)` | `default`, bridging the new `suspend route(...)` | as above |
| `MPDirectionsServiceExternalInterface` | `interface` | Kotlin implementors written as a SAM lambda. Restoring `fun` would not rescue them: the single abstract member is now `suspend route(origin, destination, config)` rather than `query(origin, destination, config, listener)`, so the lambda's shape changed regardless. Implement the interface instead. |
| `MPDirectionsServiceExternalInterface.route` / `MPDistanceMatrixServiceInterface.matrix` | `suspend` abstract members | Java implementors of either SPI. A `suspend` member compiles to a `Continuation` parameter and an `Any?` return, so neither interface is implementable from Java any more. Both first-party adapters are Kotlin. |

### MapsIndoors, MPIMapConfig and MPMapBehavior

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MapsIndoors.isAPIKeyValid()` | removed — see *Removed deprecated members* | Java + Kotlin |
| `MapsIndoors.getSolution()` / `getVenues()` / `getBuildings()` / `getCategories()` / `getUserRoles()` / `getAppliedUserRoles()` / `getAppConfig()` / `getDataSet()` / `getLocations()` / `getMapStyles()` / `getSyncedVenues()` / `getLanguage()` / `getDefaultLanguage()` / `getAvailableLanguages()` / `getImageProvider()` / `getMapsIndoorsLocationSource()` / `getMapsIndoorsVenueLocationSources()` / `getDefaultDisplayRule()` / `getMainDisplayRule()` / `getAuthToken()` | properties; the JVM accessors keep their names | Kotlin only (drop the `()`) |
| `MapsIndoors.isInitialized()` / `isReady()` / `isOfflineMode()` | `isInitialized` / `isReady` / `isOfflineMode` properties; JVM accessors unchanged | Kotlin only |
| `MapsIndoors.setOfflineMode(…)` / `setPositionProvider(…)` | property assignment (`MapsIndoors.isOfflineMode = …`); the JVM setters keep their names | Kotlin only |
| `MPIMapConfig` protected fields (`mShowFloorSelector`, `mTileFadeInEnabled`, `mShowInfoWindowOnClickedLocation`, `mShowUserPosition`, `mFloorSelector`, `mClusterIconAdapter`, `mInfoWindowAdapter`, `mTypeface`, `mFontColor`, `mShowHalo`, `mFontTextSize`, `mBuildingSelectionMode`, `mFloorSelectionMode`, `mInitialFloorIndex`, `mHiddenFeatures`, `mMapsIndoorsTransitionLevel`) | private backing behind public properties with an `internal` setter; read them through the accessors (`getShowFloorSelector()` &c.) | subclasses that touched the fields |
| `MPIMapConfig.getContext()` / `getMapProvider()` / `getMapView()` | throw `IllegalStateException` in that case instead of handing back a `null` a `@NonNull` signature promised was not — it is an object-state failure, not argument validation | callers relying on the null |
| `MPIMapConfig.Builder.mContext` / `mMapView` | `protected val` constructor properties — still readable, no longer reassignable | subclasses that reassigned them |
| `MPIMapConfig` / `MPIMapConfig.Builder` `protected` members (`mMap`, `mContext`, `mMapView`, `mapsIndoorsTransitionLevel`, `hideProviderLogo`, `hiddenFeatures`) | `protected` **properties** — a subclass declaring the same name fails to compile (`hides member of supertype and needs an 'override' modifier`), and since none are `open`, `override` is not available either | **any subclass that declares a member of one of those names — it must rename or delete it** |
| `MPMapBehavior()` | `protected` primary constructor taking `(allowFloorChange, moveCamera, animationDuration, showInfoWindow, zoomToFit, maxZoom)`; the fields are `val` properties | anyone constructing or subclassing it |
| `MPMapBehavior` protected fields (`mAllowFloorChange`, `mMoveCamera`, `mAnimationDuration`, `mShowInfoWindow`, `mZoomToFit`, `mMaxZoom`) | `val` properties; the `getZoomToFit()`/`getMoveCamera()`/… accessors are unchanged but are now **final**, so a subclass can neither write the field nor override the getter | subclasses that touched the fields or overrode a getter |

### Floor selector

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPFloorSelectorInterface`: `View getView()` | `val view: View?` | **Break — Kotlin implementers.** Java unaffected |
| `MPFloorSelectorInterface`: `boolean isAutoFloorChangeEnabled()` | `val isAutoFloorChangeEnabled: Boolean` | **Break — Kotlin implementers.** Java unaffected |

### Selection, highlight and filter behaviors

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MapControl.selectLocation(String?, MPSelectionBehavior)`: `behavior` un-annotated | `behavior` non-null | Kotlin callers passing `null` |

### Positioning and info windows

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MPDefaultInfoWindow`: `public class` | `public final class` | **Break** — a consumer can no longer subclass it |
| `MPDefaultInfoWindow`: `getInfoWindow` returns `View` | returns `View?` | Kotlin callers must handle null; Java unchanged |

### Removed deprecated members

| Member | In v5 | Who must change |
| --- | --- | --- |
| `MapControl.setMapLabelTextSize(int)` | removed — use `MPDisplayRule.setLabelStyleTextSize` | Java + Kotlin |
| `MapControl.setMapLabelFont(Typeface, String, boolean)` | removed — use the one-arg `setMapLabelFont(Typeface)` plus `MPDisplayRule.setLabelStyleTextColor` / `setLabelStyleHaloColor` | Java + Kotlin |
| `MPIMapConfig.getFontColor()` / `getShowFontHalo()` | removed — nothing ever set them | Java + Kotlin |
| `MapsIndoors.isAPIKeyValid()` | removed, with the internal implementation it fronted | Java + Kotlin |
| `MPDirectionsRenderer.setPolylineColors(int, int)` | removed — `MPDirectionsRendererOptions.animatedOverlayColor` (foreground) and `strokeColor` (background) | Java + Kotlin |
| `MPDirectionsRenderer.setAnimatedPolyline(boolean, boolean, int)` | removed with that mode — timing via `MPDirectionsRendererOptions.animationRepeating` / `animationSpeed` / `animationMinDuration`; to disable the animation, `setConfig(MPDirectionsRendererConfig(animation = Animation(type = NONE)))` | Java + Kotlin |

### Changes with no action needed

These members changed, but none of them could be reached from an app in v4, so nothing in your code needs to change.

| Area | Member | In v5 |
| --- | --- | --- |
| Route results | `MPRouteResult` equality | still identity — briefly a `data class` during review, reverted deliberately |
| Offline dataset cache | `MPDataSetCache.getSize()` | `internal val size` |
| Offline dataset cache | `UrlClient.resolveGateway(String, MPResultListener<Unit>)` | removed — that class is Kotlin now and uses the `suspend` `resolveGateway(String)` |
| Image loading | `MPImageProvider()` | `internal constructor`, with an optional loader seam; unchanged for external callers, who could never construct it |
| Tiles | `MPTileProvider.getTilesFolder()` / `getTileUrl(...)` / `getFileNameForTile(...)` | `internal` — the JVM names are mangled |
| Tiles | `MPTileProvider.getStyleFolderName()` | private |
| Tiles | `MPTileProvider(String, String, String, int, TileSize, boolean)` | **removed** — replaced by the `internal` companion factory `MPTileProvider.create(...)`, whose JVM name is mangled |
| Tiles | `FileUtils.resolveFolder(String)` | **new** — the non-creating counterpart of `getFolder(String)` |
| Tiles | `TileSize.getName()` | removed — use `Enum.name` |
| Tiles | `TileSize.getSize()` | `size` property |
| Tiles | `MPTileManager` setters | `venue` / `mapStyle` / `tileSize` / `isTileFadeInEnabled` properties |
| Tiles | `TileData` | **removed** |
| Clustering | `MPClusterIconAdapter` | `public fun interface` — same method, same arity; Java lambdas still convert, and Kotlin SAM conversion now works too |
| Clustering | `MPCluster.getId()` / `getLocations()` / `getLatLng()` / `getBounds()` / `getMPLocation()` | `id` / `locations` / `latLng` / `bounds` / `mpLocation` properties |
| Clustering | `ClusteringHelper` | `object` |
| 3D models and node labels | `NodeLabel` | `internal data class` with `val` properties |
| 3D models and node labels | `NodeLabel.index` / `NodeLabel.labels` | `index` / `labels` properties; the still-Java `Graph` reads them via `getIndex()` / `getLabels()` |
| 3D models and node labels | `NodeLabel.labels` | `HashMap<String, String>?` — explicitly nullable |
| 3D models and node labels | `Model3DManager` | unchanged |
| Pathfinding | `MPNodeData` | `internal` |
| Directions and distance-matrix services | `MPDistanceMatrixService` | `internal` |
| Directions and distance-matrix services | `MPDirectionsService.getRoute`, `routeResultReceiver` | removed |
| Route rendering | `RouteLayer` | `data class`, properties are the API |
| Route rendering | `RouteLayer.Field` | `data class` |
| Route rendering | `RouteLayerCollection` | `data class (List<RouteLayer> = emptyList())` |
| Route rendering | `MPDirectionsRenderer.loadBitmapFromUrlBlocking` | `private suspend fun loadBitmapFromUrl` |
| MapsIndoors, MPIMapConfig and MPMapBehavior | `MapsIndoors.networkOptions` / `authToken` | read-only `networkOptions` / `authToken` properties plus non-null `setNetworkOptions(…)` / `setAuthToken(…)` functions — neither value can be cleared once set |
| MapsIndoors, MPIMapConfig and MPMapBehavior | `MPMapBehavior.Builder` | Kotlin interface; nullability is in the types (`setMaxZoom(Double?)`) |
| Floor selector | `FloorSelectorManagerListener`: single-method interface | `onFloorSelected: ((MPFloor) -> Unit)?` lambda |
| Floor selector | `FloorSelectorAdapterListener`: single-method interface | `onFloorSelectionChanged: ((MPFloor) -> Unit)?` lambda |
| Floor selector | `FloorSelectorManager` / `FloorSelectorAdapter` / `MPDefaultFloorSelector`: package-private | `internal` |
| Selection, highlight and filter behaviors | `SelectionController`: `getBuildingSelectionMode()` / `setBuildingSelectionMode(…)`, and the floor pair | `buildingSelectionMode` / `floorSelectionMode` properties |
| Selection, highlight and filter behaviors | `MPSelectionBehavior` / `MPHighlightBehavior` / `MPFilterBehavior`: assigned `MPMapBehavior`'s protected fields | pass the six values through its `protected` primary constructor |
| Selection, highlight and filter behaviors | `MPSelectionBehavior.DEFAULT` &c.: `public static final` field | `@JvmField` in the companion |
| Positioning and info windows | `MPIPositionPresenter`: `boolean isVisible()` / `void setVisible(boolean)` | `var isVisible: Boolean` |
| Positioning and info windows | `MPVenueHandler`: `getBuildings()` / `getGraphs()` / `getRouteLayers()` / `getDerived*()` | `buildings` / `graphs` / `routeLayers` / `derived*` |
| Positioning and info windows | `MPUserRoleManager`: `static getsInstance()` + instance accessors | `object` + `var userRoles` |
| Positioning and info windows | `PositionProviderConfig`: `getCiscoDNAConfig()` / `getIndoorAtlasConfig()` | `ciscoDNAConfig` / `indoorAtlasConfig` |
