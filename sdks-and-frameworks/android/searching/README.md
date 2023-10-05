# Searching

Searching through your MapsIndoors data is an integral part of a great user experience with your maps. Users can look for places to go, or filter what is shown on the map.

Searches work on all MapsIndoors geodata. It is up to you to create a search experience that fits your use case. To aid you in this, there are a range of filters you can apply to the search queries to get the best results. E.g. you can filter by Categories, search only a specific part of the map or search near a Location.

All three return a list of Locations from your Solution matching the parameters they are given. The results are ranked upon the 3 following factors:

* If a "near" parameter is set, how close is the origin point to the result?
* How well does the search input text match the text of the result (using the "Levenshtein distance" algorithm)?
* Which kind of geodata is the result (e.g. Buildings are ranked over POIs)?

This means that the first item in the search result list will be the one matching the 3 factors best and so forth.

See the full list of parameters:

| Parameter  | Description                                                                                                                                 | Class    |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| take       | Max number of Locations to get                                                                                                              | MPFilter |
| Skip       | Skip the first number of entries                                                                                                            | MPFilter |
| categories | A list of Categories to limit the search to                                                                                                 | MPFilter |
| Parents    | A list of Building or Venue IDs to limit the search to                                                                                      | MPFilter |
| Types      | A list of Types to limit the search to                                                                                                      | MPFilter |
| Bounds     | Limits the result of Locations to a bounding area                                                                                           | MPFilter |
| Floor      | Limits the result of Locations to be on a specific Floor                                                                                    | MPFilter |
| Near       | Sorts the list of Locations on which Location is nearest the point given                                                                    | MPQuery  |
| Depth      | The Depth property makes it possible to get "x" amount of descendants to the given parent. The default for this is 1 (eg. Building > Floor) |          |

#### &#x20;<a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>

{% tabs %}
{% tab title="Java" %}
#### Example of Creating a Search Query <a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>

```
void findRestroom() {
    //Here we will create an empty query because we are only interrested in getting locations that match a category. If you want to be more specific here where you can add a query text like "Unisex Restroom"
    MPQuery mpQuery = new MPQuery
            .Builder()
            .build();

    List<String> categories = new ArrayList<>();
    categories.add("RESTROOMS");

    // Init the filter builder and build a filter, the criteria in this case we want maximum 50 restrooms
    MPFilter mpFilter = new MPFilter
            .Builder()
            .setCategories(categories)
            .setTake(50)
            .build();

    MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (locations, error) -> {
        //Check if there is an error and iterate through the list to do what you need with the search
    });
}
```

### Display Search Results on the Map[​](https://docs.mapsindoors.com/searching#display-search-results-on-the-map) <a href="#display-search-results-on-the-map" id="display-search-results-on-the-map"></a>

When displaying the search results it is helpful to filter the map to only show matching Locations. Matching Buildings and Venues will still be shown on the map, as they give context to the user, even if they aren't selectable on the map.

#### Example of Filtering the Map to Display Searched Locations on the Map <a href="#example-of-filtering-the-map-to-display-searched-locations-on-the-map" id="example-of-filtering-the-map-to-display-searched-locations-on-the-map"></a>



```
MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (locations, error) -> {
    if (locations != null && !locations.isEmpty()) {
        //Query with the locations from the query result. Use default camera behavior
        mMapControl.setFilter(locations, MPFilterBehavior.DEFAULT);
    }
});
```

### Clearing the Map of Your Filter[​](https://docs.mapsindoors.com/searching#clearing-the-map-of-your-filter) <a href="#clearing-the-map-of-your-filter" id="clearing-the-map-of-your-filter"></a>

After displaying the search results on your map you can then clear the filter so that all Locations show up on the map again.

#### Example of Clearing Your Map Filter to Show All Locations Again <a href="#example-of-clearing-your-map-filter-to-show-all-locations-again" id="example-of-clearing-your-map-filter-to-show-all-locations-again"></a>

```
mMapControl.clearFilter();
```
{% endtab %}

{% tab title="Kotlin" %}
#### Example of Creating a Search Query <a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>



```
fun findRestroom() {
    //Here we will create an empty query because we are only interrested in getting locations that match a category. If you want to be more specific here where you can add a query text like "Unisex Restroom"
    val mpQuery = MPQuery.Builder()
        .build()
    val categories: MutableList<String> = ArrayList()
    categories.add("RESTROOMS")

    // Init the filter builder and build a filter, the criteria in this case we want maximum 50 restrooms
    val mpFilter = MPFilter.Builder()
        .setCategories(categories)
        .setTake(50)
        .build()

    MapsIndoors.getLocationsAsync(mpQuery, mpFilter) { locations: List<MPLocation?>?, error: MIError? ->
        //Check if there is an error and iterate through the list to do what you need with the search
    }
}
```

### Display Search Results on the Map[​](https://docs.mapsindoors.com/searching#display-search-results-on-the-map) <a href="#display-search-results-on-the-map" id="display-search-results-on-the-map"></a>

When displaying the search results it is helpful to filter the map to only show matching Locations. Matching Buildings and Venues will still be shown on the map, as they give context to the user, even if they aren't selectable on the map.

#### Example of Filtering the Map to Display Searched Locations on the Map <a href="#example-of-filtering-the-map-to-display-searched-locations-on-the-map" id="example-of-filtering-the-map-to-display-searched-locations-on-the-map"></a>



```
MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (locations, error) -> {
    //Query with the locations from the query result. Use default camera behavior
    mMapControl.setFilter(locations, MPFilterBehavior.DEFAULT)
});
```

### Clearing the Map of Your Filter[​](https://docs.mapsindoors.com/searching#clearing-the-map-of-your-filter) <a href="#clearing-the-map-of-your-filter" id="clearing-the-map-of-your-filter"></a>

After displaying the search results on your map you can then clear the filter so that all Locations show up on the map again.

#### Example of Clearing Your Map Filter to Show All Locations Again <a href="#example-of-clearing-your-map-filter-to-show-all-locations-again" id="example-of-clearing-your-map-filter-to-show-all-locations-again"></a>

```
mMapControl.clearFilter()
```
{% endtab %}
{% endtabs %}

