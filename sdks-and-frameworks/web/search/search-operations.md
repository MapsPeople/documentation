# Search Operations

### Search Operations with MapsIndoors

Enhance your application's search capabilities with MapsIndoors data. The following topics offer a brief introduction to various functionalities you can implement. For in-depth guidance, click on the specific topics.

#### [Basic Searching](searching.md)

* **Retrieve Specific Location**: Use [`getLocation(id)`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocation) to obtain individual location objects.
* **Querying Locations**: Perform broader searches with [`getLocations(args)`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocations).
  * **Filter Customization**: Fine-tune your searches with specialized query parameters.

#### [Extending Your Search](../other-guides/external-ids.md) <a href="#ffaf" id="ffaf"></a>

* **Using External IDs**: Translate your unique IDs to MapsIndoors IDs using [`getLocationsByExternalId`](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/mapsindoors.services.LocationsService.html#.getLocationsByExternalId).
* **Advanced Search Integration**: Integrate MapsIndoors data into your existing search functionalities.
* **Distance Matrix**: Create a distance matrix when dealing with multiple locations. [Ref docs here](https://app.mapsindoors.com/mapsindoors/js/sdk/latest/docs/DistanceMatrixService.html)

#### [Utilizing MapsIndoors Web Components and Other Searches](utilizing-mapsindoors-web-components-and-other-searches.md)

* **Utilizing MapsIndoors web components**
  * **Components:** MapsIndoors search component, list component, etc.
  * **Click Event Handling**: Interactive retrieval of location details.
* **Venue and Building Info**: Access information about venues and buildings stored in MapsIndoors.
