# Create a Search Experience

Now you have simple app showing a map. In this step, you'll create a simple search and display the search results in a list. You'll also learn how to filter the data displayed on the map based on the search results.

### Create a Simple Query Search[​](https://docs.mapsindoors.com/getting-started/android/v3/search#create-a-simple-query-search) <a href="#create-a-simple-query-search" id="create-a-simple-query-search"></a>

Start by creating a new activity or _fragment_ to facilitate searches on your application. Here we will be using a _fragment_ for search and show to search results on, while using a bottom sheet to display the results. We also create a search input field on our main map _activity_ for the user to input the text they want to search for. This is already setup in the basic example app.

To perform a search you will need to have initiated `MapsIndoors`. This was shown in the previous section of the getting started tutorial how you do this.

For advanced usage of the search functionality read the Search guide and tutorials connected to it: [Search Guide](https://docs.mapsindoors.com/searching/)

### Show a List of Search Results[​](https://docs.mapsindoors.com/getting-started/android/v3/search#show-a-list-of-search-results) <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

Create a search method that takes a search string as a parameter on your `MapsActivity` class. In this example we only use the `setTake` on the `MPFilter` to limit our result to 30 locations. We will expand on this method later.

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L170-L216)

```
void search(String searchQuery) {
    //Query with a string to search on
    MPQuery mpQuery = new MPQuery.Builder().setQuery(searchQuery).build();
    //Filter for the search query, only taking 30 locations
    MPFilter mpFilter = new MPFilter.Builder().setTake(30).build();
    //Query for the locations
    MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (list, miError) -> {
      //Implement UI handling of the search result here
    });
}
```

To be able to search we will use a text input field where a user can write what they want to search for. This is placed at the top of the MapsActivity

To call our search method with the text in the search input field, we then add an `EditorActionListener` and a `OnClickListener` to the text input field and the search button in the `onCreate` of `MapsActivity`. Find the full `onCreate` example here: [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L53-L117) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L106)

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L70-L91)

```
protected void onCreate(Bundle savedInstanceState) {
    ...
    mSearchBtn = findViewById(R.id.search_btn);
    mSearchTxtField = findViewById(R.id.search_edit_txt);
    ...
    //ClickListener to start a search, when the user clicks the search button
    mSearchBtn.setOnClickListener(view -> {
        if (mSearchTxtField.getText().length() != 0) {
            //There is text inside the search field. So lets do the search.
            search(mSearchTxtField.getText().toString());
        }
    });

    //Listener for when the user searches through the keyboard
    mSearchTxtField.setOnEditorActionListener((textView, i, keyEvent) -> {
        if (i == EditorInfo.IME_ACTION_DONE || i == EditorInfo.IME_ACTION_SEARCH) {
            if (textView.getText().length() != 0) {
                //There is text inside the search field. So lets do the search.
                search(textView.getText().toString());
            }
            return true;
        }
        return false;
    });
    ...
}
```

Find the full `onCreate` example here: [MapsActivity.java](https://github.com/MapsIndoors/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L53-L117) or [MapsActivity.kt](https://github.com/MapsIndoors/MapsIndoors-Getting-started-android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L42-L106)

To accompany this we use the `SearchFragment` that is already created for you and a `BottomSheet` to handle the `SearchFragment`.

Observe that the `SearchFragment`is just a simple _fragment_ with a `RecyclerView` and a `SearchItemAdapter` added to it

* Java
* Kotlin

[SearchFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/SearchFragment.java)

```
public class SearchFragment extends Fragment {

    private List<MPLocation> mLocations = null;
    private MapsActivity mMapActivity = null;

    public static SearchFragment newInstance(List<MPLocation> locations, MapsActivity mapsActivity) {
        final SearchFragment fragment = new SearchFragment();
        fragment.mLocations = locations;
        fragment.mMapActivity = mapsActivity;
        return fragment;
    }

    ...

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        final RecyclerView recyclerView = (RecyclerView) view;
        recyclerView.setLayoutManager(new LinearLayoutManager(getContext()));
        recyclerView.setAdapter(new SearchItemAdapter(mLocations, mMapActivity));
    }

    ...
}
```

See the full example of `SearchFragment` here: [SearchFragment.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/SearchFragment.java) or [SearchFragment.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/SearchFragment.kt)

Create a getter for your `MapControl` object on the `MapsActivity` so that it can be used in the `SearchAdapter`.

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L119-L125)

```
public MapControl getMapControl() {
    return mMapControl;
}
```

Inside the `SearchItemAdapter` implement logic to display the locations you get from a search result. Here we show an image of the location marker and show the name of the locations.

* Java
* Kotlin

[SearchItemAdapter.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/SearchItemAdapter.java)

```
class SearchItemAdapter extends RecyclerView.Adapter<ViewHolder> {
    private final List<MPLocation> mLocations;
    private final MapsActivity mMapActivity;

    ...

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        //Setting the the text on the text view to the name of the location
        holder.text.setText(mLocations.get(position).getName());

        if (mMapActivity != null) {
            //We start by checking if there is a specific Location icon assigned to the location
            LocationDisplayRule locationDisplayRule = mMapActivity.getMapControl().getDisplayRule(mLocations.get(position));

            if (locationDisplayRule != null && locationDisplayRule.getIcon() != null) {
                //There is a specific icon on this location so we use that
                mMapActivity.runOnUiThread(()-> {
                    holder.imageView.setImageBitmap(locationDisplayRule.getIcon());
                });
            }else {
                //Location does not have a specific displayRule, we instead use type Display rule
                LocationDisplayRule typeDisplayRule = mMapActivity.getMapControl().getDisplayRule(mLocations.get(position).getType());

                if (typeDisplayRule != null) {
                    mMapActivity.runOnUiThread(()-> {
                        holder.imageView.setImageBitmap(typeDisplayRule.getIcon());
                    });
                }
            }
        }
    }

    ...
}
class ViewHolder extends RecyclerView.ViewHolder {
    final TextView text;
    final ImageView imageView;

    ViewHolder(LayoutInflater inflater, ViewGroup parent) {
        super(inflater.inflate(R.layout.fragment_search_list_item, parent, false));
        text = itemView.findViewById(R.id.text);
        imageView = itemView.findViewById(R.id.location_image);
    }
}
```

See the full example of `SearchItemAdapter` and accompanying `ViewHolder` here: [SearchItemAdapter.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/SearchItemAdapter.java#L16-L75) or [SearchItemAdapter.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/SearchItemAdapter.kt#L12-L60)

We have already implemented the BottomSheet in the UI. Now we add the search _fragment_ to the `BottomSheet` in our search query method on our `MapsActivity`. You can use the `addFragmentToBottomSheet` too add the created _fragment_ to the `BottomSheet`. When we have received the search results

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L170-L216)

```
void search(String searchQuery) {
    //Query with a string to search on
    MPQuery mpQuery = new MPQuery.Builder().setQuery(searchQuery).build();
    //Filter for the search query, only taking 30 locations
    MPFilter mpFilter = new MPFilter.Builder().setTake(30).build();

    //Query for the locations
    MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (list, miError) -> {
        //Check if there is no error and the list is not empty
        if (miError == null && !list.isEmpty()) {
            //Create a new instance of the search fragment
            mSearchFragment = SearchFragment.newInstance(list, this);
            //Make a transaction to the bottomsheet
            addFragmentToBottomSheet(mSearchFragment);
            //Clear the search text, since we got a result
            mSearchTxtField.getText().clear();
            ...
        }
    });
}
```

See the full example of the search method here: [MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L185-L234) or [MapsActivity.kt](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin/MapsActivity.kt#L137-L183)

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/android/v3/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

When getting a search result, you might want to only show those search results on the map. You can do this through calling `displaySearchResults(List<MPLocation> locations)` on `MapControl`. This method has different parameters to make it easier for you as a developer to fit your exact need in terms of animation and more. This can be read in the [JavaDoc of `MapControl`](https://app.mapsindoors.com/mapsindoors/reference/android/v3/com/mapsindoors/mapssdk/MapControl.html).

The standard implementation animates the camera to fit all Locations on the map and show the info window of a Location, if it's a list of only one Location.

When you are done showing the search results you can call `clearMap()` on `MapControl`.

Since the default `displaySearchResults(List<MPLocation> locations)` uses camera animation we will call it from the UI Thread and implement it in our search method inside the getLocationsAsync result with the list from the method.

* Java
* Kotlin

[MapsActivity.java](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/blob/master/app/src/main/java/com/example/mapsindoorsgettingstarted/MapsActivity.java#L191-L193)

```
void search(String searchQuery) {
    ...
    MapsIndoors.getLocationsAsync(mpQuery, mpFilter, (list, miError) -> {
        ...
        //Calling displaySearch results on the ui thread as camera movement is involved
        runOnUiThread(()-> {
            mMapControl.displaySearchResults(list, true);
        });
    });
}
```

Expected result:

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/android\_search\_gif.gif)

The accompanying UI and implementation of this search experience can be found in the getting started app sample. [Getting Started App sample](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android/tree/master/app/src/main/java/com/example/mapsindoorsgettingstarted) or [Getting Started App sample kotlin](https://github.com/MapsPeople/MapsIndoors-Getting-Started-Android-Kotlin/blob/main/app/src/main/java/com/example/mapsindoorsgettingstartedkotlin).

[\
](https://docs.mapsindoors.com/getting-started/android/v3/map)
