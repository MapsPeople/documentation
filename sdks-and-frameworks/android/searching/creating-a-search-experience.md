# Creating a Search Experience

This is an example of creating a simple search experience using MapsIndoors. We will create a map with a search button that leads to another Fragment that handles the search and selection. On selection of a location, we go back to the map and shows the selected location on the map.

First create a Fragment or Activity with a map and MapsIndoors loaded.

We will create a Fragment that will contain a textInput field and a RecyclerView that will show a list of `MPLocations` received from the search.

```kotlin
class FullscreenSearchFragment : Fragment() {
    // ...
}
```

As we will be using a `RecyclerView` we will need to create a `RecyclerView Adapter` to show our Location results. In this guide we will hijack the Adapter from the Template app:

```kotlin
class MPSearchItemRecyclerViewAdapter(
    private val scope: CoroutineScope,
) : RecyclerView.Adapter<MPSearchItemRecyclerViewAdapter.ViewHolder>() {
    private var locations: List<MPLocation> = emptyList()
    private var onLocationSelected: ((MPLocation) -> Unit)? = null

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder =
        ViewHolder(FragmentSearchItemBinding.inflate(LayoutInflater.from(parent.context), parent, false))

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val item = locations[position]
        getTypeIcon(item)?.let { iconUrl ->
            // loadImage is a suspend function that loads the image off the main thread
            scope.launch {
                MapsIndoors.imageProvider.loadImage(iconUrl).bitmap?.let { holder.icon.setImageBitmap(it) }
            }
        }
        holder.nameView.text = item.name
        if (item.floorName != null && item.buildingName != null) {
            val buildingName = MapsIndoors.buildings?.getBuilding(item.point.latLng)?.name ?: item.buildingName
            holder.subTextView.text = "Floor: ${item.floorName} - $buildingName"
        } else {
            holder.subTextView.visibility = View.GONE
        }
        holder.itemView.setOnClickListener {
            onLocationSelected?.invoke(item)
        }
    }

    private fun getTypeIcon(mpLocation: MPLocation): String? =
        MapsIndoors.solution?.types
            ?.firstOrNull { type -> mpLocation.type.equals(type.name, ignoreCase = true) }
            ?.icon

    fun setOnLocationSelectedListener(listener: (MPLocation) -> Unit) {
        onLocationSelected = listener
    }

    override fun getItemCount(): Int = locations.size

    fun setLocations(locations: List<MPLocation>) {
        this.locations = locations
        notifyDataSetChanged()
    }

    fun clear() {
        locations = emptyList()
        notifyDataSetChanged()
    }

    inner class ViewHolder(binding: FragmentSearchItemBinding) : RecyclerView.ViewHolder(binding.root) {
        val icon: ImageView = binding.locationIconView
        val nameView: TextView = binding.locationName
        val subTextView: TextView = binding.locationSubtext

        override fun toString(): String = "${super.toString()} '${subTextView.text}'"
    }
}
```

Setup member variables for `FullscreenSearchFragment`:

* A RecyclerView to contain the locations
* The Adapter and LayoutManager for the RecyclerView
* Some view components
* The coroutine `Job` of a pending search

```kotlin
private lateinit var recyclerView: RecyclerView
private lateinit var linearLayoutManager: LinearLayoutManager
private lateinit var searchAdapter: MPSearchItemRecyclerViewAdapter
private lateinit var searchInputTextView: TextInputEditText
private var searchJob: Job? = null
```

Init and setup the `RecyclerView` and its Adapter:

```kotlin
recyclerView = binding.searchList
linearLayoutManager = LinearLayoutManager(requireContext())
// The adapter loads icons in coroutines that are cancelled with the fragment's view
searchAdapter = MPSearchItemRecyclerViewAdapter(viewLifecycleOwner.lifecycleScope)
recyclerView.apply {
    layoutManager = linearLayoutManager
    adapter = searchAdapter
}
```

Init and setup the view components to handle searching inside the `onViewCreated`

```kotlin
searchInputTextView = binding.searchInputEditText
val imm = requireContext().getSystemService(InputMethodManager::class.java)
searchInputTextView.addTextChangedListener { text ->
    scheduleSearch(text)
}
searchInputTextView.setOnEditorActionListener { textView, actionId, _ ->
    if (actionId == EditorInfo.IME_ACTION_DONE || actionId == EditorInfo.IME_ACTION_SEARCH) {
        if (textView.text.isNotEmpty()) {
            search(textView.text.toString())
        }
        // Making sure keyboard is closed.
        imm.hideSoftInputFromWindow(textView.windowToken, 0)
        return@setOnEditorActionListener true
    }
    false
}
```

Create a method that executes a search once the user has stopped typing. It launches a coroutine in the view's `lifecycleScope` and cancels the previous one, so typing quickly only results in one search:

```kotlin
private fun scheduleSearch(text: CharSequence?) {
    // Cancel the pending search, so we only search when the user has stopped typing for a second
    searchJob?.cancel()
    searchJob = viewLifecycleOwner.lifecycleScope.launch {
        delay(1_000)
        if (text != null && text.length >= 2) {
            search(text.toString())
        }
    }
}
```

Add a listener to the Adapter for when a user selects a location, to navigate back to the map and show the selected location. Here we use navigation together with a bundle to tell the other fragment of the selected location:

```kotlin
searchAdapter.setOnLocationSelectedListener { location ->
    val bundle = bundleOf("locationId" to location.locationId)
    findNavController().navigate(R.id.action_nav_search_fullscreen_to_nav_search, bundle)
}
```

[See the sample in FullscreenSearchFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/search/FullscreenSearchFragment.kt) Now we will implement the `FullscreenSearchFragment` together with our Fragment or Activity containing a MapsIndoors Map. Add a Button to open the `FullscreenSearchFragment` inside your Activity or Fragment view and a assign a Click listener to it.

```kotlin
binding.searchButton.setOnClickListener {
    openSearchFragment()
}
```

Create the `openSearchFragment` method to navigate to the `FullScreenSearchFragment`

```kotlin
private fun openSearchFragment() {
    findNavController().navigate(R.id.action_nav_search_to_nav_search_fullscreen)
}
```

Finally create a way to handle the selected location when a user is navigated to your fragment again. How this example is set up the Map will be reloaded when navigated to it. Therefor we will handle the selection after `MapControl` is created.

```kotlin
MapControl.create(mapConfig) { mapControl, error ->
    if (mapControl == null) {
        Log.e("SearchFragment", "Could not create MapControl: ${error?.message}")
        return@create
    }
    this.mapControl = mapControl
    val locationId = arguments?.getString("locationId")
    if (locationId != null) {
        mapControl.selectLocation(locationId, MPSelectionBehavior.DEFAULT)
    } else {
        // No location was selected, so get the default venue (in the White House solution the only one)
        val bounds = MapsIndoors.venues?.defaultVenue?.bounds ?: return@create
        // The callback can arrive on a background thread, so move the camera from the main thread
        viewLifecycleOwner.lifecycleScope.launch {
            // Animates the camera to fit the venue
            googleMap.animateCamera(
                CameraUpdateFactory.newLatLngBounds(
                    LatLngBoundsConverter.toLatLngBounds(bounds),
                    19
                )
            )
        }
    }
}
```

[See the sample in SearchFragment.kt](https://github.com/MapsPeople/MapsIndoors-Android-Examples/blob/main/MapsIndoorsSamples/app/src/main/java/com/mapspeople/mapsindoorssamples/ui/search/SearchFragment.kt)
