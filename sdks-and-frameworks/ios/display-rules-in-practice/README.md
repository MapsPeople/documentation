---
description: iOS v4
---

# Display Rules in Practice

here are two ways to change the appearance of the map content in MapsIndoors and Google Maps.

* Using Display Rules
* Using Google Maps styling (or Mapbox for Android and Web)

Each has its own purpose which will be explained below.

### Style the Map using Display Rules on iOS[​](https://docs.mapsindoors.com/display-rules-in-practice#style-the-map-using-display-rules-on-ios) <a href="#style-the-map-using-display-rules-on-ios" id="style-the-map-using-display-rules-on-ios"></a>

In the [MapsIndoors CMS](https://cms.mapsindoors.com/types) you can set display rules for the different types of locations in your MapsIndoors content. The changes you make in the CMS will take effect whenever your app reloads or when you call `MPMapsIndoors.shared.synchronise()` within the app session.

A Display Rule encapsulates both what, how and when a Location should be displayed on the map. A Location is presented on the map using a combination of icon, text and polygon. Each of these can appear at different, independent ranges of zoom-levels. For example a venue can appear as a marker-icon on low zoom-levels, when zooming in the venue name can appear, and zooming even more in the venue polygon can appear.

In some cases, you may also want to programmatically set display rules that define when and how to show a location. Display rules are defined in a Display Rule object. These Display Rule objects are tied to a solution, and can be modified at runtime.

You can modify display rules programatically in multiple ways depending on your use case:

* Modify the Display Rule for the Selected Location
* Set a Display Rule for a type of Location
* Set a Display Rule for a single specific Location
* Set a Display Rule for multiple Locations

#### Modify the Display Rule for the Selected Location[​](https://docs.mapsindoors.com/display-rules-in-practice#modify-the-display-rule-for-the-selected-location-2) <a href="#modify-the-display-rule-for-the-selected-location-2" id="modify-the-display-rule-for-the-selected-location-2"></a>

When a Location is selected it is highlighted using the Display Rule found through `MPMapsIndoors.shared.displayRuleFor(displayRuleType: .selectionHighlight)`. You can change the values of the Display Rule like any other DisplayRule.

```swift
let selectionDisplayRule = MPMapsIndoors.shared.displayRuleFor(displayRuleType: .selectionHighlight)
selectionDisplayRule?.polygonStrokeColor = .blue
selectionDisplayRule?.polygonFillColor = .green
selectionDisplayRule?.polygonStrokeWidth = 8
```

#### Setting Display Rule for a Type[​](https://docs.mapsindoors.com/display-rules-in-practice#setting-display-rule-for-a-type-2) <a href="#setting-display-rule-for-a-type-2" id="setting-display-rule-for-a-type-2"></a>

To set new display rules for a type of Locations, you need to know the types of Locations in your Location dataset, so you may look these up in the CMS. The types can also be retrieved in code with `MPMapsIndoors.shared.solution.types`. The display rule name corresponds to the Location Type we want the rule to apply for. So in order to style a specific type of Location differently, just put in the type name as the Display Rule name.

```swift
if let infoDisplayRule = MPMapsIndoors.shared.displayRuleFor(type: "Office") {
    infoDisplayRule.icon = UIImage(named : "office")
    infoDisplayRule.zoomFrom = 17
}
```

#### Setting Display Rule for a Single and Multiple Locations[​](https://docs.mapsindoors.com/display-rules-in-practice#setting-display-rule-for-a-single-and-multiple-locations-2) <a href="#setting-display-rule-for-a-single-and-multiple-locations-2" id="setting-display-rule-for-a-single-and-multiple-locations-2"></a>

To set new display rules for a Location, you need to have the Location at hand. Locations can be fetched e.g. using `MPMapsIndoors.shared.locationWith(locationId:)`. Once you have a location, you can set a custom display rule for it.

```swift
let myLocation = MPMapsIndoors.shared.locationWith(locationId: "MyLocationId")
let myDisplayRule = MPMapsIndoors.shared.displayRuleFor(location: myLocation)
myDisplayRule.icon = myImage
myDisplayRule.zoomFrom = 15
```

For multiple Locations, you can fetch a list of Locations using `locationsWith(query:filter:)`:

```swift
let query = MPQuery()
query.query = "lounge"
let locations = await MPMapsIndoors.shared.locationsWith(query: query, filter: MPFilter())
for location in locations {
    let dr = MPMapsIndoors.shared.displayRuleFor(location: location)
    dr?.icon = UIImage(named: "chair")
}
```

#### Presenting Locations Using Polygons[​](https://docs.mapsindoors.com/display-rules-in-practice#presenting-locations-using-polygons-2) <a href="#presenting-locations-using-polygons-2" id="presenting-locations-using-polygons-2"></a>

To present a polygon, either configure the Display Rule using the CMS, or configure a Display Rule programatically:

```swift
let polygonDisplayRule = MPMapsIndoors.shared.displayRuleFor(type: "Office")
polygonDisplayRule?.polygonVisible = true
polygonDisplayRule?.polygonZoomFrom = 12
polygonDisplayRule?.polygonZoomTo = 21
polygonDisplayRule?.polygonStrokeWidth = 2
polygonDisplayRule?.polygonStrokeColor = .yellow
polygonDisplayRule?.polygonFillColor = .yellow.withAlphaComponent(0.45)
```

### Style the Map using Google Maps Styling[​](https://docs.mapsindoors.com/display-rules-in-practice#style-the-map-using-google-maps-styling-1) <a href="#style-the-map-using-google-maps-styling-1" id="style-the-map-using-google-maps-styling-1"></a>

MapsIndoors is built on top of Google Maps or Mapbox which each have their own way of styling the map. Google Maps styling will only affect the MapsIndoors map if Google Maps has Points of Interest placed inside or near the buildings that you build a MapsIndoors solution for. By default, MapsIndoors applies a Google Maps styling that hides most POI icons that may collide with MapsIndoors content.

You can apply your own styling to Google Maps using `mapStyle` on `GMSMapView`.

```swift
myGoogleMapView.mapStyle = GMSMapStyle(jsonString: myStyleJSON)
```

The JSON string that you apply in this case can be built using the [Google Maps Styling Wizard](https://mapstyle.withgoogle.com/). Read more about styling the Google Map in the [Google Maps iOS SDK Docs](https://developers.google.com/maps/documentation/ios-sdk/styling).
