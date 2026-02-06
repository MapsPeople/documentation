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

#### Example of Creating a Search Query <a href="#example-of-creating-a-search-query" id="example-of-creating-a-search-query"></a>

{% tabs %}
{% tab title="Java" %}

{% code overflow="wrap" lineNumbers="true" %}
```java
void findRestroom() {
    //Here we will create an empty query because we are only interested in getting locations that match a category. If you want to be more specific here where you can add a query text like "Unisex Restroom"
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
{% endcode %}

{% endtab %}

{% tab title="Kotlin" %}

{% code overflow="wrap" lineNumbers="true" %}
```kotlin
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
{% endcode %}

{% endtab %}

{% endtabs %}

### Display Search Results on the Map[​](https://docs.mapsindoors.com/searching#display-search-results-on-the-map) <a href="#display-search-results-on-the-map" id="display-search-results-on-the-map"></a>

When displaying the search results it is helpful to filter the map to only show matching Locations. Matching Buildings and Venues will still be shown on the map, as they give context to the user, even if they aren't selectable on the map.

#### Example of Filtering the Map to Display Searched Locations on the Map <a href="#example-of-filtering-the-map-to-display-searched-locations-on-the-map" id="example-of-filtering-the-map-to-display-searched-locations-on-the-map"></a>

{% tabs %}

{% tab title="Java" %}

{% code overflow="wrap" lineNumbers="true" %}
```java
MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (locations, error) -> {
    if (locations != null && !locations.isEmpty()) {
        //Query with the locations from the query result. Use default camera behavior
        mMapControl.setFilter(locations, MPFilterBehavior.DEFAULT);
    }
});
```
{% endcode %}

{% endtab %}

{% tab title="Kotlin" %}

{% code overflow="wrap" lineNumbers="true" %}
```kotlin
MapsIndoors.getLocationsAsync(mpQuery, mpFilter) { locations, error ->
    //Query with the locations from the query result. Use default camera behavior
    mMapControl.setFilter(locations, MPFilterBehavior.DEFAULT)
};
```
{% endcode %}

{% endtab %}

{% endtabs %}

### Clearing the Map of Your Filter[​](https://docs.mapsindoors.com/searching#clearing-the-map-of-your-filter) <a href="#clearing-the-map-of-your-filter" id="clearing-the-map-of-your-filter"></a>

After displaying the search results on your map you can then clear the filter so that all Locations show up on the map again.

#### Example of Clearing Your Map Filter to Show All Locations Again <a href="#example-of-clearing-your-map-filter-to-show-all-locations-again" id="example-of-clearing-your-map-filter-to-show-all-locations-again"></a>

{% tabs %}

{% tab title="Java" %}

{% code overflow="wrap" lineNumbers="true" %}
```java
mMapControl.clearFilter();
```
{% endcode %}

{% endtab %}

{% tab title="Kotlin" %}

{% code overflow="wrap" lineNumbers="true" %}
```kotlin
mMapControl.clearFilter()
```
{% endcode %}

{% endtab %}

{% endtabs %}

### Searching for Nested Categories <a href="#searching-for-nested-categories" id="searching-for-nested-categories"></a>

If your solution uses nested categories, searching for them is now much more intuitive. When you search for a parent category, all of its sub-categories are automatically included in the search – no extra setup is required.

#### Understanding Nested Categories

Suppose you have three categories: `Restroom`, `Unisex`, and `Handicap`. Here, `Unisex` and `Handicap` are sub-categories of `Restroom`. This hierarchy allows you to organize your data more flexibly and makes searching more powerful.

Before searching, you might want to inspect the category structure in the SDK. For example, you can check if a category has specific children, or list all sub-categories:

{% code overflow="wrap" lineNumbers="true" %}
```kotlin
val categoryCollection = MapsIndoors.getCategories()

// check whether a category has a named child category
val isChild = categoryCollection?.getCategory("Restroom")?.childKeys?.contains("Unisex")
if (isChild == true) {
    print("Restroom has a child with the key 'Unisex'!")
}

// fetch sub-categories with the child keys
val keys = categoryCollection?.getCategory("Restroom").childKeys
for (key in keys ?: emptyList()) {
    val category = categoryCollection?.getCategory(key)
    if (category != null) {
        print("Restroom has category ${category.value} as a child!")
    }
}
```
{% endcode %}

#### Searching with Nested Categories

When you perform a search for a parent category, such as `Restroom`, the SDK will automatically include all its sub-categories (`Unisex`, `Handicap`, etc.) in the search results. This means you only need to specify the parent category in your filter, and all relevant locations will be found.

{% code overflow="wrap" lineNumbers="true" %}
```kotlin
// we are only searching for the category, so lets set an empty query
val query = MPQuery.Builder().build()
// add the super-category Restroom to the list of categories
val filter = MPFilter.Builder().setCategories(listOf("Restroom")).build()

MapsIndoors.getLocationsAsync(query, filter) { locations, error -> 

    // lets see if we have found any Locations that have the type "Unisex"
    if (locations?.any { loc -> loc.categories?.any { cat -> cat == "Unisex" } == true } == true) {
        // success
        print("Locations with Unisex category found")
    }
}
```
{% endcode %}


> **Note:** When you search for a parent category, all of its sub-categories are always included in the results, there is no way to limit the search to only the parent category itself. If you need more granular control (for example, to only return locations with a specific sub-category), consider organizing your categories so that each search target has its own unique sub-category. This way, you can search for exactly the locations you want by specifying the appropriate sub-category in your filter.