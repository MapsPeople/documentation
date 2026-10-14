# Configuring a menu with AppConfig

`AppConfig` contains elements that are useful for any app developer using the SDK. This guide will showcase how to use `AppConfig` to create a menu of location categories.

> This guide uses code from the "Getting Started" guide. If you have not completed it yet, it is highly recommended to complete it in order to achieve a better understanding of the MapsIndoors SDK. The guide can be found [here](https://docs.mapsindoors.com/getting-started/android/). If you just want to follow this guide, the getting started code can be found [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin).

First, make a new fragment for the menu, and call it `MenuFragment`, this fragment will take a list of `MPMenuInfo` which will become the elements of the menu.

> If you have not followed the getting started guide, the code for the `fragment_search` layout can be found in the [getting started app](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin).

```kotlin
class MenuFragment : Fragment() {
    private var menuInfos: List<MPMenuInfo> = emptyList()
    private lateinit var mapActivity: MapsActivity

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        // For the brevity of this guide, we will reuse the bottom sheet used in the searchFragment
        return inflater.inflate(R.layout.fragment_search, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val recyclerView = view as RecyclerView
        recyclerView.layoutManager = LinearLayoutManager(context)
        recyclerView.adapter = MenuItemAdapter(menuInfos, mapActivity, viewLifecycleOwner.lifecycleScope)
    }

    companion object {
        fun newInstance(menuInfos: List<MPMenuInfo>, mapsActivity: MapsActivity): MenuFragment =
            MenuFragment().apply {
                this.menuInfos = menuInfos
                mapActivity = mapsActivity
            }
    }
}
```

Next make an adapter for this list of `MPMenuInfo`. In this example the adapter reuses the `ViewHolder` created for the search experience in the getting started guide. For now, Just set the name of the item in `onBindViewHolder`, the icon and click listener will be set later.

> If you have not followed the getting started guide, the code can be seen [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt#L53-L62).

```kotlin
internal class MenuItemAdapter(
    private val menuInfos: List<MPMenuInfo>,
    private val mapActivity: MapsActivity,
    private val scope: CoroutineScope,
) : RecyclerView.Adapter<ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(LayoutInflater.from(parent.context), parent)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val menuInfo = menuInfos[position]
        // Setting the text on the text view to the name of the menu item
        holder.text.text = menuInfo.name
        // ...
    }

    override fun getItemCount(): Int = menuInfos.size
}
```

The icon for the `MPMenuInfo` is saved as an URL, as such it has to be downloaded to be displayed. Launch a coroutine in the fragment's view lifecycle scope, download the image as a stream into a `Bitmap` on the IO dispatcher, then update the item's view back on the main thread.

> This is a very rudimentary example, and should not be replicated in production code, see it as an exercise to implement a better way to download and display the icons.

```kotlin
// If there is an icon for this menu item, use it
val iconUrl = menuInfo.iconUrl
if (iconUrl != null) {
    scope.launch {
        // As we need to download the image, it has to be offloaded from the main thread
        val image = withContext(Dispatchers.IO) {
            runCatching { URL(iconUrl).openStream().use(BitmapFactory::decodeStream) }.getOrNull()
        }
        // Back on the main thread, set the image
        image?.let { holder.imageView.setImageBitmap(it) }
    }
}
```

To show which locations belong to the category of the selected `MPMenuInfo`, call `MapsIndoors.getLocationsAsync(query, filter, listener)`. The query can be `null` as nothing specific needs to be queried. Set the category on the `MPFilter`, this has to be the category key `MPMenuInfo.categoryKey`, as this key is shared with locations in the SDK.

Finally, in the listener call `MapControl.setFilter(locations, behavior)` as this will filter the map to only show the selected locations.

```kotlin
// When a category is selected, we want to filter the map so it only shows the locations in that
// category
holder.itemView.setOnClickListener {
    // Filter on the selected category key. We do not need to query anything specific, so no MPQuery is passed
    val filter = MPFilter {
        categories = listOf(menuInfo.categoryKey)
    }
    MapsIndoors.getLocationsAsync(null, filter) { locations, error ->
        if (error == null && locations != null) {
            mapActivity.mapControl.setFilter(locations, MPFilterBehavior.DEFAULT)
        }
    }
}
```

Then, in order to show the menu, hijack the search icon's `onClickListener` method. The `MPMenuInfo` list can be fetched via `MapsIndoors.appConfig?.getMenuInfo(menuName)`, and will give a menu corresponding to the given name, in this guide `"mainmenu"` has been used as it is the default.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    // Use the search button to open the menu instead of starting a search
    val searchButton = findViewById<ImageButton>(R.id.search_btn)
    searchButton.setOnClickListener {
        val menuInfos = MapsIndoors.appConfig?.getMenuInfo("mainmenu") ?: return@setOnClickListener
        menuFragment = MenuFragment.newInstance(menuInfos, this)
        // Make a transaction to the bottom sheet
        addFragmentToBottomSheet(menuFragment)
    }
    // ...
}
```

Finally to clear the category filter from the map call `MapControl.clearFilter()`, in this example it is called when closing the menu sheet, it the `onDestroyView` method of `MenuFragment`.

```kotlin
override fun onDestroyView() {
    // When we close the menu fragment we want to display all locations again, not just whichever were selected last
    mapActivity.mapControl.clearFilter()
    super.onDestroyView()
}
```

The code shown in this guide can be found [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/tree/feature/appconfig).
