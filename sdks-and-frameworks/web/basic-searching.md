---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# Basic Searching

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
| Depth      | The Depth property makes it possible to get "x" amount of descendants to the given parent. The default for this is 1 (eg. Building > Floor) | MPFilter |

#### Example of Creating a Search Query[​](https://docs.mapsindoors.com/searching#example-of-creating-a-search-query) <a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>

See the full list of parameters in the [reference guide](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations), or below:

```
const searchParameters = {
  q: 'Office',
  near: { lat: 38.897579747054046, lng: -77.03658652944773 }, // // Blue Room, The White House
  take: 1
}

mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
  console.log(locations);
});
```

## Display Search Results on the Map[​](https://docs.mapsindoors.com/searching#display-search-results-on-the-map)

When displaying the search results it is helpful to filter the map to only show matching Locations. Matching Buildings and Venues will still be shown on the map, as they give context to the user, even if they aren't selectable on the map.

#### Example of Filtering the Map to Display Searched Locations on the Map  <a href="#example-of-filtering-the-map-to-display-searched-locations-on-the-map" id="example-of-filtering-the-map-to-display-searched-locations-on-the-map"></a>

```
const searchParameters = {
  q: 'Office',
  near: { lat: 38.897579747054046, lng: -77.03658652944773 }, // // Blue Room, The White House
  take: 1
}

mapsindoors.services.LocationsService.getLocations(searchParameters).then(locations => {
  mapsIndoorsInstance.filter(locations.map(location => location.id), false);
});
```

### Clearing the Map of Your Filter[​](https://docs.mapsindoors.com/searching#clearing-the-map-of-your-filter) <a href="#clearing-the-map-of-your-filter" id="clearing-the-map-of-your-filter"></a>

After displaying the search results on your map you can then clear the filter so that all Locations show up on the map again.

#### Example of Clearing Your Map Filter to Show All Locations Again <a href="#example-of-clearing-your-map-filter-to-show-all-locations-again" id="example-of-clearing-your-map-filter-to-show-all-locations-again"></a>

```
mapsIndoorsInstance.filter(null);
```

#### Display Locations as List[​](https://docs.mapsindoors.com/searching#display-locations-as-list) <a href="#display-locations-as-list" id="display-locations-as-list"></a>

You can also search for Locations, and have them presented to you as a list, instead of just displaying them on the map.

The full code example is shown in the JSFiddle below, which will be examined below.

#### Search[​](https://docs.mapsindoors.com/searching#search) <a href="#search" id="search"></a>

The `mapsindoors.services.LocationsService` class exposes the `getLocations` function that enables you to search for Locations.

It will return a Promise that gets resolved when the query has executed.

See [mapsindoors.services.LocationsService](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html) for more information.

```
searchElement.addEventListener('input', debounce((e) => {
    const value = e.target.value;
    if (value > '') {
        mapsindoors.services.LocationsService.getLocations({ q: value, includeOutsidePOI: true })
            .then(displayResults)
            .then(filterMap);
    } else {
        clearResults();
        clearFilter();
    }
}, 500));
```

The `debounce` method is there to ensure that the service is not being called in rapid succession. This method delays the execution of the function by 500ms, unless `debounce` is called again within 500ms, in which case the timer is reset.

See this article ["What is debouncing" by Jamis Charles](https://medium.com/@jamischarles/what-is-debouncing-2505c0648ff1) for a more detailed description of the `debounce` concept.

When the function executes, we check whether the input is empty or not. A request object is created if the input is not empty.

The `getLocations` function expects either no input, in which case it returns all Locations, or an Object (please refer to the official documentation for an exhaustive list of properties). In this case, the constant `value` is passed to the `q` property and the `includeOutsidePOI` property is set to `true`. When the Promise resolves, the response is passed to the `displayResults` helper function.

If the input is empty, we clear the result list and reset the map filter by calling the helper functions `clearResults` and `clearFilter`.

#### Checking for Results[​](https://docs.mapsindoors.com/searching#checking-for-results) <a href="#checking-for-results" id="checking-for-results"></a>

We need to clear the previous results, and check if any Locations were returned. If so, we loop through them and add them to the result list.

```
function displayResults(locations) {
    clearResults();

    if (locations.length > 0) {
        for (const location of locations) {
            searchResults.innerHTML += `<li>${location.properties.name}</li>`;
        }
    } else {
        searchResults.innerHTML = '<li class="no-results">No results matched the query.</li>';
    }

    return locations;
}
```

If no Locations are returned, a message is shown to the user stating "No results matched the query.". Otherwise, we pass the Locations on to the next helper function called `filterMap`.

```
function filterMap(locations) {
    mapsIndoors.filter(locations.map(location => location.id), false);
    return locations;
}
```

The purpose of the `filterMap` function is to create a list of `location id`s used to filter the Locations on the map.

The second parameter tells MapsIndoors not to change the viewport of the map.

For more information, see `MapsIndoors.filter` in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#filter).
