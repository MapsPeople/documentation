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

# Searching

Searching through your MapsIndoors data is an key part of a great user experience with your maps. Users can look for places to go, or filter what is shown on the map.

Searches work on all MapsIndoors geodata. It is up to you to create a search experience that fits your use case.&#x20;

## Retrieve Specific Location: `getLocation(id)`

### **Use-Case 1: Center the Map to the Location**

#### Example 1: Using MapsIndoors Location ID on the Card

This example assumes that you've stored the MapsIndoors Location ID directly on the card, accessible via a custom attribute or some other mechanism. When the card is clicked, `getLocation(id)` retrieves the corresponding location, centers the map on it, and sets the zoom level.

```javascript
card.addEventListener('click', () => {
  const locationId = card.getAttribute('data-location-id'); // Assume the MapsIndoors ID is stored in a data attribute
  mapsindoors.services.LocationsService.getLocation(locationId).then(location => {
    mapsIndoorsInstance.setFloor(location.properties.floor);
    mapInstance.setCenter({
      lat: location.properties.anchor.coordinates[1],
      lng: location.properties.anchor.coordinates[0]
    });
    mapInstance.setZoom(18);
  });
});
```

#### Example 2: Using an External Custom ID on the Card

In this example, an external (custom) ID is stored on the card. The `getLocationsByExternalId` method is used to fetch locations, taking into account that multiple locations could be returned.

```javascript
card.addEventListener('click', () => {
  const externalId = card.getAttribute('data-external-id'); // Assume the external ID is stored in a data attribute
  
  // Use getLocationsByExternalId to fetch locations by their external IDs
  mapsindoors.services.LocationsService.getLocationsByExternalId(externalId).then(locations => {
    if (locations.length > 0) {
      const location = locations[0]; // Take the first location if multiple are returned
      
      // Set the floor and center the map
      mapsIndoorsInstance.setFloor(location.properties.floor);
      mapInstance.setCenter({
        lat: location.properties.anchor.coordinates[1],
        lng: location.properties.anchor.coordinates[0]
      });
      
      mapInstance.setZoom(18);
    } else {
      // Handle the case where no locations are returned for the given external ID
      console.warn(`No locations found for external ID ${externalId}`);
    }
  });
});
```

Both examples work off a click event attached to a card element. The key difference lies in the method used to query MapsIndoors for the location information. Example 1 uses the native MapsIndoors ID, while Example 2 uses an external ID. Both methods then focus the map on the retrieved location.

### **Use-Case 2: Utilizing MapsIndoors SDK Highlighting and Filtering**

Take advantage of using MapsIndoors native filtering and highlighting functionality via the SDK without needing to implement your own custom display logic.



DESCRIPTION OF BOTH FILTERING AND HIGHLIGHTING WILL GO HERE

IMAGE\_PLACEHOLDER\_HERE

HOLDING OFF ON THIS UNTIL I GET MORE DETAILS ON HOW THESE METHODS WORK

### **Use-Case 3: Modify Display Rule**

#### Example 1: Customizing Display Rule on Click Event with MapsIndoors Location ID

```javascript
// Define the rule outside the listener for better readability and reusability
const rule = { 
  visible: true,
  polygonVisible: true,
  polygonFillColor: "#FF0000",
  polygonFillOpacity: 1,
  iconSize: { width: 30, height: 30 },
  labelVisible: true,
};

let previousLocationId = null;  // Keep track of previously filtered location

card.addEventListener('click', () => {
  const locationId = card.getAttribute('data-location-id');
  mapsindoors.services.LocationsService.getLocation(locationId).then(location => {
    // Optionally set the floor and center the map
    mapsIndoorsInstance.setFloor(location.properties.floor);
    mapInstance.setCenter({
      lat: location.properties.anchor.coordinates[1],
      lng: location.properties.anchor.coordinates[0]
    });

    // Reset display rule for previously filtered location, if any
    if (previousLocationId) {
      mapsIndoorsInstance.setDisplayRule(previousLocationId, null);
    }

    // Apply the new display rule to the clicked location
    mapsIndoorsInstance.setDisplayRule(location.id, rule);

    // Update the previous location ID
    previousLocationId = location.id;
  });
});
```

#### Example 2: Using an External Custom ID

```javascript
// Define the display rule
const rule = { 
  visible: true,
  polygonVisible: true,
  polygonFillColor: "#FF0000",
  polygonFillOpacity: 1,
  iconSize: { width: 30, height: 30 },
  labelVisible: true,
};

let previousLocationId = null;  // Keep track of the previously filtered location

card.addEventListener('click', () => {
  const externalId = card.getAttribute('data-external-id');
  
  // Use getLocationsByExternalId() to fetch locations by their external IDs
  mapsindoors.services.LocationsService.getLocationsByExternalId(externalId).then(locations => {
    if (locations.length > 0) {
      const location = locations[0];  // Assume the first location is the one to display

      // Optionally set the floor and center the map
      mapsIndoorsInstance.setFloor(location.properties.floor);
      mapInstance.setCenter({
        lat: location.properties.anchor.coordinates[1],
        lng: location.properties.anchor.coordinates[0]
      });

      // Reset display rule for the previously filtered location, if any
      if (previousLocationId) {
        mapsIndoorsInstance.setDisplayRule(previousLocationId, null);
      }

      // Apply the new display rule to the location
      mapsIndoorsInstance.setDisplayRule(location.id, rule);

      // Update the ID of the previously filtered location
      previousLocationId = location.id;

    } else {
      console.warn(`No locations found for external ID ${externalId}`);
    }
  });
});
```

This code assumes that if multiple locations are returned from `getLocationByExternalId()`, the first one is the relevant location for display. The remainder of the code remains largely similar to the previous example, but we're fetching the locations based on an external ID instead of a MapsIndoors Location ID.

### Use-Case 4: Add a Popup / Info Window

For this use-case, we're focusing on how to display an information popup or info window when a MapsIndoors location is clicked. Both Mapbox and Google Maps implementations are provided. These popups will show a custom image and a link, allowing developers the flexibility to populate it with any content. Let's assume you're getting back your location object from one of the approaches in [Use-Case 1](searching.md#use-case-1-center-the-map-to-the-location).

**Mapbox Implementation**

In the Mapbox example, we add an event listener to the MapsIndoors instance that listens for 'click' events. When a location is clicked, we display a Mapbox popup at the location's coordinates. Any previously displayed popup will be removed to avoid clutter.

```javascript
let currentPopup = null;

export const handleLocationClickForMapbox = (location, mapsIndoorsInstance, mapInstance) => {
  if (currentPopup) {
    currentPopup.remove();
  }

  const coords = location.properties.anchor.coordinates;
  const popupContent = `
    <img src="${'Your_custom_image_url_here'}" alt="${location.properties.description}" width="100" height="100" />
    <h2><a href="${'Your_custom_link_here'}" target="_blank">${location.properties.name}</a></h2>
  `;

  currentPopup = new mapboxgl.Popup({ closeOnClick: true, closeButton: true })
    .setLngLat(coords)
    .setHTML(popupContent)
    .addTo(mapInstance);
};

mapsIndoorsInstance.addListener('click', location => {
  handleLocationClickForMapbox(location, mapsIndoorsInstance, mapInstance);
});
```

**Google Maps Implementation**

In the Google Maps example, we add an event listener to the MapsIndoors instance to listen for location clicks. When a location is clicked, an info window appears at the coordinates of that location. Similar to the Mapbox example, any previously displayed info window will be closed.

```javascript
let previousInfoWindow = null;

mapsIndoorsInstance.addListener('click', location => {
  if (previousInfoWindow) {
    previousInfoWindow.close();
  }

  const coords = location.properties.anchor.coordinates;
  const infoWindowContent = `
    <img src="${'Your_custom_image_url_here'}" alt="${location.properties.description}" width="100" height="100" />
    <h2><a href="${'Your_custom_link_here'}" target="_blank">${location.properties.name}</a></h2>
  `;

  const infoWindowOptions = {
    content: infoWindowContent,
    position: {
      lat: coords[1],
      lng: coords[0]
    }
  };

  const infoWindow = new google.maps.InfoWindow(infoWindowOptions);
  infoWindow.open(googleMapsInstance);
  previousInfoWindow = infoWindow;
});
```

Both implementations ensure that only one popup or info window is open at a given time, closing any previous ones when a new location is clicked. This keeps the map clean and focuses the user's attention on the most recently clicked location.



## Retrieve Queried Locations: `getLocations(`args opt`)`

To help you in this, there is a range of filters you can apply to the search queries to get the best results. E.g. you can filter by Categories, search only a specific part of the map or search near a Location.

All three return a list of Locations from your solution matching the parameters they are given. The results are ranked upon the three following factors:

* If a "near" parameter is set, how close is the origin point to the result?
* How well does the search input text match the text of the result?&#x20;
  * (Our base algorithm for searching is using the "[Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein\_distance)" algorithm)
* Which kind of geodata is the result (e.g. Buildings are ranked over POIs)?

This means that the first item in the search result list will be the one best matching the three factors. **You always have the ability to reorder your array of locations based on your preference before rendering them in your user interface, if you choose to handle that via some client-side code.**

Feel free to refer to this table for a comprehensive understanding of each parameter's type, optional/required status, and functionality.&#x20;

| Parameter    | Type                    | Optional/Required  | Description                                                                                                     | Default / Example | Code Example                                                                                                                |
| ------------ | ----------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `q`          | string                  | Optional           | Use a text query to search for one or more locations.                                                           |                   |                                                                                                                             |
| `fields`     | string                  | Optional           | Fields to search in when using the search string parameter 'q'. Options: "name,description,aliases,categories"  |                   |                                                                                                                             |
| `types`      | Array.                  | Optional           | Filter by types in a comma-separated list. A location only has one type.                                        |                   | `mapsindoors.services.LocationsService.getLocations({ lr: 'en', types: ['meetingroom'] }).then(locations => { ... });`      |
| `categories` | Array.                  | Optional           | Filter by categories in a comma-separated list. A location can be in multiple categories.                       |                   | `mapsindoors.services.LocationsService.getLocations({ categories: categoryKey, lr: 'en' }).then(locations => { ... });`     |
| `bbox`       | Object                  | Optional           | Limits the result to inside the bounding box. Must include `bbox.east`, `bbox.north`, `bbox.south`, `bbox.west` |                   |                                                                                                                             |
| `bbox.east`  | number                  | Required if `bbox` | Max longitude of the bounds in degrees.                                                                         |                   |                                                                                                                             |
| `bbox.north` | number                  | Required if `bbox` | Max latitude of the bounds in degrees.                                                                          |                   |                                                                                                                             |
| `bbox.south` | number                  | Required if `bbox` | Min latitude of the bounds in degrees.                                                                          |                   |                                                                                                                             |
| `bbox.west`  | number                  | Required if `bbox` | Min longitude of the bounds in degrees.                                                                         |                   |                                                                                                                             |
| `take`       | number                  | Optional           | Max number of locations to get.                                                                                 |                   | `await mapsindoors.services.LocationsService.getLocations({ near: 'location:9897fd93fcb14bd39ec8110d', take: 5, ... });`    |
| `skip`       | number                  | Optional           | Skip the first number of entries.                                                                               |                   |                                                                                                                             |
| `near`       | LatLngLiteral \| string | Optional           | Can either be a coordinate `{lat: number, lng: number}` or a string in the format "type:id".                    |                   | `await mapsindoors.services.LocationsService.getLocations({ near: 'location:9897fd93fcb14bd39ec8110d', radius: 50, ... });` |
| `radius`     | number                  | Optional           | A radius in meters. Must be supplied when using `near` with a point.                                            |                   | `await mapsindoors.services.LocationsService.getLocations({ near: 'location:9897fd93fcb14bd39ec8110d', radius: 50, ... });` |
| `floor`      | integer                 | Optional           | Filter locations to a specific floor.                                                                           |                   | `await mapsindoors.services.LocationsService.getLocations({ near: 'location:9897fd93fcb14bd39ec8110d', floor: 50, ... });`  |
| `orderBy`    | string                  | Optional           | Which property the result should be sorted by.                                                                  |                   |                                                                                                                             |
| `sortOrder`  | string                  | Optional           | Specifies in which order the results are sorted, either "ASC" or "DESC"                                         | "ASC"             |                                                                                                                             |
| `building`   | string                  | Optional           | Limit the search for locations to a building.                                                                   |                   |                                                                                                                             |
| `venue`      | string                  | Optional           | Limit the search for locations to a venue (id or name).                                                         |                   |                                                                                                                             |

### Example of Creating a Search Query[​](https://docs.mapsindoors.com/searching#example-of-creating-a-search-query) <a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>

See the full list of parameters in the [reference guide](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations):

```javascript
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

When displaying the search results, it is helpful to filter the map to only show matching Locations. Matching Buildings and Venues will still be shown on the map, as they give context to the user, even if they aren't selectable on the map.

#### Example of Filtering the Map to Display Searched Locations on the Map <a href="#example-of-filtering-the-map-to-display-searched-locations-on-the-map" id="example-of-filtering-the-map-to-display-searched-locations-on-the-map"></a>

```javascript
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

```javascript
mapsIndoorsInstance.filter(null);
```

### Display Locations as List[​](https://docs.mapsindoors.com/searching#display-locations-as-list) <a href="#display-locations-as-list" id="display-locations-as-list"></a>

You can also search for Locations, and have them presented to you as a list, instead of just displaying them on the map.

The full code example is shown in the JSFiddle below which will be examined below.

#### Search example[​](https://docs.mapsindoors.com/searching#search) <a href="#search" id="search"></a>

The `mapsindoors.services.LocationsService` class exposes the `getLocations` function that enables you to search for Locations.

It will return a promise that gets resolved when the query has executed.

See [mapsindoors.services.LocationsService](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html) for more information.

```javascript
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

```javascript
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

```javascript
function filterMap(locations) {
    mapsIndoors.filter(locations.map(location => location.id), false);
    return locations;
}
```

The purpose of the `filterMap` function is to create a list of `location id`s used to filter the Locations on the map.

The second parameter tells MapsIndoors not to change the viewport of the map.

For more information, see `MapsIndoors.filter` in the [reference documentation](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/MapsIndoors.html#filter).
