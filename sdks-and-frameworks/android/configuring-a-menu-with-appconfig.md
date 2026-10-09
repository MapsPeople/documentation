# Configuring a menu with AppConfig

`AppConfig` contains elements that are useful for any app developer using the SDK. This guide will showcase how to use `AppConfig` to create a menu of location categories.

> This guide uses code from the "Getting Started" guide. If you have not completed it yet, it is highly recommended to complete it in order to achieve a better understanding of the MapsIndoors SDK. The guide can be found [here](https://docs.mapsindoors.com/getting-started/android/). If you just want to follow this guide, the getting started code can be found [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin).

First, make a new fragment for the menu, and call it `MenuFragment`, this fragment will take a list of `MPMenuInfo` which will become the elements of the menu.

> If you have not followed the getting started guide, the code for the `fragment_search` layout can be found in the [getting started app](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin).

```kotlin
class MenuFragment : Fragment() {
    private lateinit var mMenuInfos: List<MPMenuInfo?>
    private lateinit var mMapActivity: MapsActivity


    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        // For the brevity of this guide, we will reuse the bottom sheet used in the searchFragment
        return inflater.inflate(R.layout.fragment_search, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        val recyclerView = view as RecyclerView
        recyclerView.layoutManager = LinearLayoutManager(context)
        recyclerView.adapter = MenuItemAdapter(mMenuInfos, mMapActivity)
    }

    companion object {
        fun newInstance(menuInfos: List<MPMenuInfo?>, mapsActivity: MapsActivity): MenuFragment {
            val fragment = MenuFragment()
            fragment.mMenuInfos = menuInfos
            fragment.mMapActivity = mapsActivity
            return fragment
        }
    }
}
```

Next make an adapter for this list of `MPMenuInfo`. In this example the adapter reuses the `ViewHolder` created for the search experience in the getting started guide. For now, Just set the name of the item in `onBindViewHolder`, the icon and click listener will be set later.

> If you have not followed the getting started guide, the code can be seen [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt#L53-L62).

```kotlin
internal class MenuItemAdapter(
    private val mMenuInfos: List<MPMenuInfo?>,
    private val mMapActivity: MapsActivity?
) : RecyclerView.Adapter<ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(LayoutInflater.from(parent.context), parent)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        //Setting the the text on the text view to the name of the location
        holder.text.text = mMenuInfos[position]?.name
        ...

    }

    override fun getItemCount(): Int {
        return mMenuInfos.size
    }
}
```

The icon for the `MPMenuInfo` is saved as an URL, as such it has to be downloaded to be displayed. Open a network connection in a background thread and download the image as a stream and save it in a `Bitmap`, then update the item's view back on the Main thread.

> This is a very rudimentary example, and should not be replicated in production code, see it as an exercise to implement a better way to download and display the icons.

```kotlin
// if there exists an icon for this menuItem, then we will use it
val iconUrl = mMenuInfos[position]?.iconUrl
if (iconUrl != null) {
    // As we need to download the image, it has to be offloaded from the main thread
    Thread {
        val image: Bitmap = try {
            val url = URL(iconUrl)
            BitmapFactory.decodeStream(url.openConnection().getInputStream())
        } catch (ignored: IOException) {
            return@Thread
        }
        //Set the image while on the main thread
        Handler(Looper.getMainLooper()).post {
            holder.imageView.setImageBitmap(
                image
            )
        }
    }.start()
}
```

To show which locations belong to the category of the selected `MPMenuInfo`, call `MapsIndoors.getLocationsAsync(MPQuery, MPFilter, OnLocationsReadyListener)`. The `MPQuery` can just be empty as nothing specific needs to be queried. Set the category on the `MPFilter`, this has to be the category key `MPMenuInfo.getCategoryKey()`, as this key is shared with locations in the SDK.

Finally, in the `OnLocationsReadyListener` call `MapControl.setFilter(List<MPLocation>, MPFilterBehavior)` as this will filter the map to only show the selected locations.

```kotlin
// When a category is selected, we want to filter the map s.t. it only shows the locations in that
// category
holder.itemView.setOnClickListener { view ->
    // empty query, we do not need to query anything specific
    val query = MPQuery.Builder().build()
    // filter created on the selected category key
    val filter = MPFilter.Builder().setCategories(
        listOf(
            mMenuInfos[position]?.categoryKey
        )
    ).build()
    MapsIndoors.getLocationsAsync(
        query, filter
    ) { locations: List<MPLocation?>?, error: MIError? ->
        if (error == null && locations != null) {
            mMapActivity?.getMapControl()?.setFilter(locations, MPFilterBehavior.DEFAULT)
        }
    }
}
```

Then, in order to show the menu, hijack the search icon's `onClickListener` method. The `MPMenuInfo` can be fetched via `MapsIndoors.getAppConfig().getMenuInfo(String)`, and will give a menu corresponding to the inputted String, in this guide `"mainmenu"` has been used as it is the default.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    ...
    //ClickListener to start a search, when the user clicks the search button
    var searchBtn = findViewById<ImageButton>(R.id.search_btn)
    searchBtn.setOnClickListener {
        /*
        if (mSearchTxtField.text?.length != 0) {
            //There is text inside the search field. So lets do the search.
            search(mSearchTxtField.text.toString())
        }
        //Making sure keyboard is closed.
        imm.hideSoftInputFromWindow(it.windowToken, 0)
        */

        // Lets hijack the searchbutton
        mMenuFragment = MenuFragment.newInstance(MapsIndoors.getAppConfig()?.getMenuInfo("mainmenu")!!, this)
        //Make a transaction to the bottomsheet
        addFragmentToBottomSheet(mMenuFragment)
    }
    ...
}
```

Finally to clear the category filter from the map call `MapControl.clearFilter()`, in this example it is called when closing the menu sheet, it the `onDestroyView` method of `MenuFragment`.

```kotlin
override fun onDestroyView() {
    // When we close the menu fragment we want to display all locations again, not just whichever were selected last
    mMapActivity.getMapControl().clearFilter()
    super.onDestroyView()
}
```

The code shown in this guide can be found [here](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/tree/feature/appconfig).
