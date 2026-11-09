---
description: >-
  Now you have simple app showing a map. In this step, you'll create a simple
  search and display the search results in a list. You'll also learn how to
  filter the data displayed on the map based on the
---

# Create a Search Experience

## Create a Simple Query Search​ <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

Start by creating a new activity or _fragment_ to facilitate searches on your application. Here we will be using a _fragment_ for search and show to search results on, while using a bottom sheet to display the results. We also create a search input field on our main map _activity_ for the user to input the text they want to search for. This is already setup in the basic example app.

To perform a search you will need to have initiated [`MapsIndoors`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-maps-indoors/index.html?query=class%20MapsIndoors). This was shown in the previous section of the getting started tutorial how you do this.

For advanced usage of the search functionality read the Search guide and tutorials connected to it: [Search Guide](https://docs.mapsindoors.com/searching/)

## Show a List of Search Results​ <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

Create a search method that takes a search string as a parameter on your `MapsActivity` class. In this example we only set [`take` on the `MPFilter`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-m-p-filter/index.html?query=open%20class%20MPFilter) to limit our result to 30 locations. We will expand on this method later.

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L133-L172)

```kotlin
private fun search(searchQuery: String) {
    // Query with a string to search on
    val mpQuery = MPQuery { query = searchQuery }
    // Filter for the search query, only taking 30 locations
    val mpFilter = MPFilter { take = 30 }

    // Query for the locations
    MapsIndoors.getLocationsAsync(mpQuery, mpFilter) { locations: List<MPLocation>?, error: MIError? ->
        // Implement UI handling of the search result here
    }
}
```

To be able to search we will use a text input field where a user can write what they want to search for. This is placed at the top of the MapsActivity

To call our search method with the text in the search input field, we then add an `EditorActionListener` and a `OnClickListener` to the text input field and the search button in the `onCreate` of `MapsActivity`. Find the full `onCreate` example here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L105)

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L56-L81)

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    searchTxtField = findViewById(R.id.search_edit_txt)
    // Listener for when the user searches through the keyboard
    searchTxtField.setOnEditorActionListener { textView, actionId, _ ->
        if (actionId == EditorInfo.IME_ACTION_DONE || actionId == EditorInfo.IME_ACTION_SEARCH) {
            if (textView.text.isNotEmpty()) {
                search(textView.text.toString())
            }
            return@setOnEditorActionListener true
        }
        false
    }

    // ClickListener to start a search, when the user clicks the search button
    val searchBtn = findViewById<ImageButton>(R.id.search_btn)
    searchBtn.setOnClickListener {
        if (!searchTxtField.text.isNullOrEmpty()) {
            // There is text inside the search field. So lets do the search.
            search(searchTxtField.text.toString())
        }
    }
    // ...
}
```

Find the full `onCreate` example here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L105)

To accompany this we use the `SearchFragment` that is already created for you and a `BottomSheet` to handle the `SearchFragment`.

Observe that the `SearchFragment`is just a simple _fragment_ with a `RecyclerView` and a `SearchItemAdapter` added to it

[SearchFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/SearchFragment.kt#L14-L37)

```kotlin
class SearchFragment : Fragment() {
    private var locations: List<MPLocation> = emptyList()
    private var mapActivity: MapsActivity? = null

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val recyclerView = view as RecyclerView
        recyclerView.layoutManager = LinearLayoutManager(context)
        recyclerView.adapter = SearchItemAdapter(locations, mapActivity)
    }

    // ...

    companion object {
        fun newInstance(locations: List<MPLocation>, mapsActivity: MapsActivity?): SearchFragment =
            SearchFragment().apply {
                this.locations = locations
                this.mapActivity = mapsActivity
            }
    }
}
```

See the full example of `SearchFragment` here: [SearchFragment.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/SearchFragment.kt)

Expose your [`MapControl` object](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html?query=class%20MapControl) on the `MapsActivity` as a property with a private setter, so that it can be used in the `SearchItemAdapter`.

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L174-L176)

```kotlin
lateinit var mapControl: MapControl
    private set
```

Inside the `SearchItemAdapter` implement logic to display the locations you get from a search result. Here we show an image of the location marker and show the name of the locations.

[SearchItemAdapter.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt)

```kotlin
internal class SearchItemAdapter(
    private val locations: List<MPLocation>,
    private val mapActivity: MapsActivity?,
) : RecyclerView.Adapter<ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder =
        ViewHolder(LayoutInflater.from(parent.context), parent)

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val location = locations[position]
        holder.text.text = location.name

        val activity = mapActivity ?: return
        MapsIndoors.getDisplayRule(location)?.getIconAsync { bitmap, _ ->
            // The icon can arrive on a background thread, so set it from the main thread
            activity.lifecycleScope.launch {
                holder.imageView.setImageBitmap(bitmap)
            }
        }
    }

    override fun getItemCount(): Int = locations.size
}

internal class ViewHolder(inflater: LayoutInflater, parent: ViewGroup?) :
    RecyclerView.ViewHolder(inflater.inflate(R.layout.fragment_search_list_item, parent, false)) {
    val text: TextView = itemView.findViewById(R.id.text)
    val imageView: ImageView = itemView.findViewById(R.id.location_image)
}
```

See the full example of `SearchItemAdapter` and accompanying `ViewHolder` here: [SearchItemAdapter.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt)

We have already implemented the BottomSheet in the UI. Now we add the search _fragment_ to the `BottomSheet` in our search query method on our `MapsActivity`. You can use the `addFragmentToBottomSheet` too add the created _fragment_ to the `BottomSheet`. When we have received the search results

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L133-L172)

```kotlin
private fun search(searchQuery: String) {
    // Query with a string to search on
    val mpQuery = MPQuery { query = searchQuery }
    // Filter for the search query, only taking 30 locations
    val mpFilter = MPFilter { take = 30 }

    // Query for the locations
    MapsIndoors.getLocationsAsync(mpQuery, mpFilter) { locations, error ->
        // Check if there is no error and the list is not empty
        if (error == null && !locations.isNullOrEmpty()) {
            // Create a new instance of the search fragment
            val fragment = SearchFragment.newInstance(locations, this)
            // The result can arrive on a background thread, so update the UI from the main thread
            lifecycleScope.launch {
                searchFragment = fragment
                // Make a transaction to the bottom sheet
                addFragmentToBottomSheet(fragment)
                // Clear the search text, since we got a result
                searchTxtField.text?.clear()
                // ...
            }
        }
    }
}
```

See the full example of the search method here: [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt)

## Filter Locations on Map Based on Search Results​ <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

When getting a search result, you might want to only show those search results on the map. You can do this through [calling `setFilter(locations, behavior)` on `MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/set-filter.html). The `MPFilterBehavior` parameter makes it easier for you as a developer to fit your exact need in terms of animation and more. This can be read in the [reference documentation of `MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/index.html).

The standard implementation animates the camera to fit all Locations on the map and show the info window of a Location, if it's a list of only one Location.

When you are done showing the search results you can [call `clearFilter()` on `MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v5/MapsIndoorsSDK/com.mapsindoors.core/-map-control/clear-filter.html).

Since `setFilter` with `MPFilterBehavior.DEFAULT` uses camera animation, we will call it from the main thread with `lifecycleScope` and implement it in our search method inside the `getLocationsAsync` result with the list from the method.

[MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/Google_Maps/mapsindoorsgettingstartedkotlin/src/main/java/com/mapspeople/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L150)

```kotlin
private fun search(searchQuery: String) {
    val mpQuery = MPQuery { query = searchQuery }
    val mpFilter = MPFilter { take = 30 }

    MapsIndoors.getLocationsAsync(mpQuery, mpFilter) { locations, error ->
        // Calling setFilter on the main thread as camera movement is involved
        lifecycleScope.launch {
            mapControl.setFilter(locations.orEmpty(), MPFilterBehavior.DEFAULT)
        }
    }
}
```

Expected result:

<figure><img src="../../../.gitbook/assets/android_search_gif.gif" alt=""><figcaption></figcaption></figure>

The accompanying UI and implementation of this search experience can be found in the getting started app sample. [Getting Started App sample](https://github.com/MapsPeople/MapsIndoors-Android-Examples/tree/main/Google_Maps/mapsindoorsgettingstartedkotlin)
