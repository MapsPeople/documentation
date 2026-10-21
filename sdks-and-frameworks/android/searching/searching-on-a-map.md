# Searching on a Map

Use the `MapsIndoors.getLocationsAsync()` method to search for content in your MapsIndoors Solution.

#### Setup a query for the nearest single best matching location and display the result on the map​ <a href="#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map" id="setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map"></a>

```kotlin
// Build a query, in this case we will query for coffee machines
val query = MPQuery {
    query = "coffee machine"
}
// Build a filter, the criteria in this case we want 1 coffee machine from the 1st floor
val filter = MPFilter {
    take = 1
    floorIndex = 1
}
// Query the data
MapsIndoors.getLocationsAsync(query, filter) { locations, error ->
    if (!locations.isNullOrEmpty()) {
        mapControl.setFilter(locations, MPFilterBehavior.DEFAULT)
    }
}
```

#### Setup a query for a group of locations and display the result on the map​ <a href="#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map" id="setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map"></a>

```kotlin
// Build a query, in this case we will query for all toilets
val query = MPQuery {
    query = "Toilet"
}
// Build a filter, the criteria in this case we want maximum 50 toilets from the 1st floor
val filter = MPFilter {
    take = 50
    floorIndex = 1
}
// Query the data
MapsIndoors.getLocationsAsync(query, filter) { locations, error ->
    if (!locations.isNullOrEmpty()) {
        mapControl.setFilter(locations, MPFilterBehavior.DEFAULT)
    }
}
```

Please note that you are not guaranteed that the visible floor contains any search results, so that is why we change floor in the above example.
