# Searching on a Map

Use the `MapsIndoors.getLocationsAsync()` method to search for content in your MapsIndoors Solution.

#### Setup a query for the nearest single best matching location and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map) <a href="#setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map" id="setup-a-query-for-the-nearest-single-best-matching-location-and-display-the-result-on-the-map"></a>

```java
// Init the query builder and build a query, in this case we will query for coffee machines ***/
MPQuery query = new MPQuery.Builder().
        setQuery("coffee machine").
        build();
// Init the filter builder and build a filter, the criteria in this case we want 1 coffee machine from the 1st floor
MPFilter filter = new MPFilter.Builder().
        setTake(1).
        setFloorIndex(1).
        build();
// Query the data
MapsIndoors.getLocationsAsync( query, filter, ( locs, err ) -> {
    if( locs != null && locs.size() != 0 ) {
        mMapControl.setFilter( locs, MPFilterBehavior.DEFAULT );
    }
} );
```

#### Setup a query for a group of locations and display the result on the map[​](https://docs.mapsindoors.com/search-on-map#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map) <a href="#setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map" id="setup-a-query-for-a-group-of-locations-and-display-the-result-on-the-map"></a>

```java
// Init the query builder and build a query, in this case we will query for all to toilets
MPQuery query = new MPQuery.Builder().
        setQuery("Toilet").
        build();
// Init the filter builder and build a filter, the criteria in this case we want maximum 50 toilets from the 1st floor
MPFilter filter = new MPFilter.Builder().
        setTake( 50 ).
        setFloorIndex( 1 ).
        build();
// Query the data
MapsIndoors.getLocationsAsync(query, filter, (locs, err) -> {
    if(locs != null && locs.size() != 0 ){
        mMapControl.setFilter( locs, MPFilterBehavior.DEFAULT );
    }
});
```

Please note that you are not guaranteed that the visible floor contains any search results, so that is why we change floor in the above example.
