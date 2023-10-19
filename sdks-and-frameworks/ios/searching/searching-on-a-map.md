# Searching on a Map



{% tabs %}
{% tab title="iOS v4" %}
Use the [`MPMapsIndoors.shared.locationsWith(query:filter:)`](https://app.mapsindoors.com/mapsindoors/reference/ios/4.1.3/documentation/mapsindoors/mapsindoorsshared/locationswith\(query:filter:\)) method to search for content in your MapsIndoors Solution.

#### Setup a query for the nearest single best matching Location and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-1) <a href="#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-1" id="setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-1"></a>

```swift
let filter = MPFilter()
let query = MPQuery()
query.query = "Office"
query.near = MPPoint(lat: 57.057964, lon: 9.9504112)
query.take = 1

let locations = await MPMapsIndoors.shared.locationsWith(query: query, filter: filter)
if let location = locations?.first {
    self.mapControl?.goTo(entity: location)
}

```

#### Setup a query for a group of Locations and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-1) <a href="#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-1" id="setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-1"></a>

```swift
let filter = MPFilter()
let query = MPQuery()
query.categories = ["Office"]
query.max = 50

let locations = await MPMapsIndoors.shared.locationsWith(query: query, filter: filter)
self.mapControl?.setFilter(locations: locations, behavior: .default)
if let location = locations?.first {
    self.mapControl?.currentFloor = location.floor
}
```

Please note that you are not guaranteed that the visible floor contains any search results, so that is why we change floor in the above example.
{% endtab %}

{% tab title="iOS v3" %}
Use the `MPLocationService` class to search for content in your MapsIndoors Solution.

#### Setup a query for the nearest single best matching Location and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-2) <a href="#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-2" id="setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map-2"></a>

```swift
let filter = MPFilter()
let query = MPQuery()
query.query = "Office"
query.near = MPPoint(lat: 57.057964, lon: 9.9504112)
query.take = 1
MPLocationService.sharedInstance().getLocationsUsing(query, filter: filter) { (locations, error) in
    if error == nil {
        let location = locations?.first
        self.mapControl?.go(to: location)
    }
}
```

#### Setup a query for a group of Locations and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-2) <a href="#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-2" id="setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map-2"></a>

```swift
let filter = MPFilter()
let query = MPQuery()
query.categories = ["Office"]
query.max = 50
MPLocationService.sharedInstance().getLocationsUsing(query, filter: filter) { (locations, error) in
    if error == nil {
        self.mapControl?.searchResult = locations
        let firstLocation = locations?.first
        self.mapControl?.currentFloor = firstLocation.floor
    }
}
```

Please note that you are not guaranteed that the visible floor contains any search results, so that is why we change floor in the above example.
{% endtab %}
{% endtabs %}
