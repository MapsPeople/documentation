---
description: Android v4
---

# Display Rules in Practice

here are two ways to change the appearance of the map content in MapsIndoors and Google Maps.

* Using Display Rules
* Using Google Maps styling (or Mapbox for Android and Web)

Each has its own purpose which will be explained below.

To get an overview of what Display Rules are and can be used for, read the [Display Rules](../../products/cms/display-rules.md) page first.



{% tabs %}
{% tab title="Kotlin" %}
### Style the Map using Display Rules[​](https://docs.mapsindoors.com/display-rules-in-practice#style-the-map-using-display-rules-with-kotlin) <a href="#style-the-map-using-display-rules-with-kotlin" id="style-the-map-using-display-rules-with-kotlin"></a>

In the [MapsIndoors CMS](https://cms.mapsindoors.com/types) you can set display rules for the different types of locations in your MapsIndoors content. The changes you make in the CMS will take effect whenever your app reboots or when you call `MapsIndoors.synchroniseContent()` within the app session.

A Display Rule encapsulates both what, how and when a Location should be displayed on the map. A Location is presented on the map using a combination of icon, text and polygon. Each of these can appear at different, independent ranges of zoom-levels. For example a venue can appear as a marker-icon on low zoom-levels, when zooming in the venue name can appear, and zooming even more in the venue polygon can appear.

In some cases, you may also want to programmatically set display rules that define when and how to show a location. Display rules are defined in a Display Rule object. These Display Rule objects are tied to a solution, and can be modified at runtime.

You can modify display rules programatically in multiple ways depending on your use case:

You can set display rules programatically in multiple ways depending on your use case:

* Modify the Display Rule for the Selected Location
* Modify the Display Rule for the Building outline
* Modify a Display Rule for a type of Location
* Modify a Display Rule for a specific Location

#### Modify the Display Rule for the Selected Location[​](https://docs.mapsindoors.com/display-rules-in-practice#modify-the-display-rule-for-the-selected-location-1) <a href="#modify-the-display-rule-for-the-selected-location-1" id="modify-the-display-rule-for-the-selected-location-1"></a>

When a Location is selected, this Location is highlighted using the Display Rule name found through `MPSolutionDisplayRule.SELECTION`. You can change the values of the Display Rule like any other DisplayRule.

```kotlin
MapsIndoors.getDisplayRule(MPSolutionDisplayRule.SELECTION)?.polygonStrokeColor = blue
MapsIndoors.getDisplayRule(MPSolutionDisplayRule.SELECTION)?.polygonFillColor = green
MapsIndoors.getDisplayRule(MPSolutionDisplayRule.SELECTION)?.polygonStrokeWidth = 8f
```

#### Setting Display Rule for a Type[​](https://docs.mapsindoors.com/display-rules-in-practice#setting-display-rule-for-a-type-1) <a href="#setting-display-rule-for-a-type-1" id="setting-display-rule-for-a-type-1"></a>

To set new display rules for a single Location, you need to have the Location at hand. Locations can be fetched using `getLocationById` or searched for through `getLocationsAsync`. Once you have a location, you can modify the display rule for it.

```kotlin
MapsIndoors.getDisplayRule("Office")?.let {
    it.setIcon(R.drawable.ic_baseline_close_24, Color.BLUE)
}
```

Setting a display rule for a type will only apply to the single instance of `MapControl`.

#### Setting Display Rule for a Single and Multiple Locations[​](https://docs.mapsindoors.com/display-rules-in-practice#setting-display-rule-for-a-single-and-multiple-locations-1) <a href="#setting-display-rule-for-a-single-and-multiple-locations-1" id="setting-display-rule-for-a-single-and-multiple-locations-1"></a>

To set new display rules for a single Location, you need to have the Location at hand. Locations can be fetched using `getLocationById`. Once you have a location, you can set a custom display rule for it.

```kotlin
val mpLocation = MapsIndoors.getLocationById("MyLocationId")
MapsIndoors.getDisplayRule(mpLocation)?.setIcon(R.drawable.ic_baseline_air_24, Color.GRAY);
```

For multiple Locations, you fetch a list of Locations using `getLocationsAsync` instead:

```kotlin
MapsIndoors.getLocationsAsync(null, MPFilter.Builder().setTypes(listOf("Meetingroom")).build()) { locations, _ ->
    for (location in locations) {
        location?.let {
            MapsIndoors.getDisplayRule(it)?.setIcon(R.drawable.ic_baseline_air_24, Color.GRAY)
        }
    }
}
```

#### Presenting Locations Using Polygons[​](https://docs.mapsindoors.com/display-rules-in-practice#presenting-locations-using-polygons-1) <a href="#presenting-locations-using-polygons-1" id="presenting-locations-using-polygons-1"></a>

To present a polygon, either configure the Display Rule using the CMS, or configure a Display Rule programatically:

```kotlin
val mpDisplayRule = MapsIndoors.getDisplayRule("Office")
mpDisplayRule?.let {
    it.setIcon(R.drawable.ic_baseline_bolt_24, Color.GRAY)
    it.isPolygonVisible = true
    it.polygonZoomFrom = 12f
    it.polygonZoomTo = 21f
    it.polygonFillColor = "0000FF"
    it.polygonStrokeColor = "0000FF"
}
```

### Style the Map using Google Maps Styling with Kotlin[​](https://docs.mapsindoors.com/display-rules-in-practice#style-the-map-using-google-maps-styling-with-kotlin) <a href="#style-the-map-using-google-maps-styling-with-kotlin" id="style-the-map-using-google-maps-styling-with-kotlin"></a>

> Further documentation on the Google Maps styling can be found here: [https://developers.google.com/maps/documentation/android-sdk/styling](https://developers.google.com/maps/documentation/android-sdk/styling)

MapsIndoors is built on top of Google Maps or Mapbox which has its own way of styling the map. Google Maps styling will only affect the MapsIndoors map if Google Maps has Points of Interest placed inside or near the buildings that you build a MapsIndoors solution for. The MapsIndoors styling applies a Google Maps styling that hides most POI icons that may collide with MapsIndoors content. This can be turned on and off through the `MPMapConfig`.

You can apply your own styling to Google Maps using `googleMap.setMapStyle`.

```kotlin
googleMap.setMapStyle(MapStyleOptions.loadRawResourceStyle(this, R.raw.style_json))
```

The JSON string that you apply in this case can be built using the [Google Maps Styling Wizard](https://mapstyle.withgoogle.com/). Read more about styling the Google Map in the [Google Maps Android SDK Docs](https://developers.google.com/maps/documentation/android-sdk/styling).

### Style the Map using Mapbox Styling with Kotlin[​](https://docs.mapsindoors.com/display-rules-in-practice#style-the-map-using-mapbox-styling-with-kotlin) <a href="#style-the-map-using-mapbox-styling-with-kotlin" id="style-the-map-using-mapbox-styling-with-kotlin"></a>

> Further documentation on the Mapbox styling can be found here: [https://docs.mapbox.com/android/maps/guides/styles/set-a-style/](https://docs.mapbox.com/android/maps/guides/styles/set-a-style/)

Since version 4.11.3 it is possible to change the Mapbox style at anytime during runtime. Changing the style is done simply by calling loadStyle on a `MapboxMap` object. Read more about Mapbox styling in the link above

```kotlin
mapboxMap.loadStyle(Style.MAPBOX_STREETS)
```

On versions previous to 4.11.3 it was not possible to change the style during runtime and it was required through the `MPMapConfig` to inform that the MapsIndoors Default Style should be used, and any required style should be loaded before creating `MapControl`

```kotlin
val mapConfig = MPMapConfig.Builder(
            requireActivity(),
            mMap,
            mMapView,
            getString(R.string.mapbox_api_key),
            //Setting useMapsIndoorsStyle to false
            false
        ).build()
mapboxMap.loadStyle(Style.MAPBOX_STREETS)

MapControl.create(mapConfig) { mapControl: MapControl?, miError: MIError? ->
    if (miError == null) {
        myMapControl = mapControl
    }
}
```

#### Caveats of using a custom Mapbox style.

* If using any of the new Standard styles it is up to the developer to makes sure 3D data does not obfuscate MapsIndoors data. It will be drawn underneath Mapbox data.
* You loose the gliding transition between Mapbox 3D building data and MapsIndoors 3D data

{% endtab %}
{% endtabs %}
