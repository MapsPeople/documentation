# Create a Search Experience

Now you have simple app showing a map. In this step, you'll create a simple search and display the search results in a list. You'll also learn how to filter the data displayed on the map based on the search results.

\
Create a Simple Query Search[​](https://docs.mapsindoors.com/getting-started/flutter/search#create-a-simple-query-search)

Start by creating a new `Widget` to facilitate searches on your application. Here we will be using a `SearchWidget` for searching with, while using a `Drawer` to display the results. We also create a search input field on our `Scaffold` for the user to input the text they want to search for. This is already setup in the basic example app.

To perform a search you will need to have initiated [`MapsIndoors`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/mapsindoors-library.html#functions). This was shown in the previous section of the getting started tutorial how you do this.

For advanced usage of the search functionality read the Search guide and tutorials connected to it: [Search Guide](https://docs.mapsindoors.com/searching/), the examples are given for Android and iOS but the parameters and methods are the same in Flutter.

### Show a List of Search Results[​](https://docs.mapsindoors.com/getting-started/flutter/search#show-a-list-of-search-results) <a href="#show-a-list-of-search-results" id="show-a-list-of-search-results"></a>

Create a search method that takes a search string as a parameter in your `State` class. In this example we only use the [`setTake` on the `MPFilter`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MPFilter-class.html) to limit our result to 30 locations. We will expand on this method later.

```dart
void search(String value) {
  // we should not search when the searchbar is empty
  if (value.isEmpty) {
    return;
  }
  // make a query with the search text
  MPQuery query = (MPQueryBuilder()..setQuery(value)).build();
  // we just want to see the top 30 results, as not to be overwhelmed
  MPFilter filter = (MPFilterBuilder()..setTake(30)).build();

  // fetch all (max 30) locations that match the query
  getLocationsByQuery(query: query, filter: filter).then((locations) {
    if (locations != null && locations.isNotEmpty) {
      // add UI handling here
    }
  }).catchError((err) {
    // handle the error, for now just show a snackbar
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(
      content: Text("Search failed: $err"),
      backgroundColor: Colors.red,
    ));
  });
}
```

Next we create our own Widget to act as our search bar, this widget will take a function `onSubmitted` as a parameter, this is the function we will use to do the searcing when the user submits their search text.

```dart
class SearchWidget extends StatelessWidget {
  final Function(String val)? onSubmitted;
  const SearchWidget({
    super.key,
    this.onSubmitted,
  });

  @override
  Widget build(BuildContext context) {
    return TextField(
      decoration: const InputDecoration(
          icon: Icon(
            Icons.search,
            color: Colors.white,
          ),
          hintText: "Search...",
          hintStyle: TextStyle(color: Colors.white)),
      cursorColor: Colors.white,
      style: const TextStyle(color: Colors.white),
      onSubmitted: onSubmitted,
    );
  }
}
```

To be able to search we will use a text input field where a user can write what they want to search for. We can change the `AppBar` in our `Scaffold` to be the `SearchWidget` that we created earlier.

Now, when the user submits their text in the `SearchWidget` it will call the `search` function which we created earlier.

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    ...
    appBar: AppBar(
      // we change the titlebar into a searching widget
      title: SearchWidget(
        onSubmitted: search,
      ),
    ),
    ...
  );
}
```

We now need some UI that can show the result of our search, for this we will use a `Drawer` which we can attach to our `Scaffold`. As the _drawer_ is built directly in the build tree we can use a shared variable `_searchResults` which we will populate when a search is performed.

```dart
List<MPLocation> _searchResults = [];

@override
Widget build(BuildContext context) {
  return Scaffold(
    ...
    drawer: Drawer(
      child: Flex(
        direction: Axis.vertical,
        children: [
          Expanded(
            child: _searchResults.isNotEmpty
            ? ListView.builder(
                itemBuilder: (ctx, i) {
                  return ListTile(
                    onTap: () {
                      // when clicking on a location in the search results we will close the drawer and open a bottom sheet with that locations details
                      _mapControl.selectLocation(_searchResults[i]);
                      _scaffoldKey.currentState?.closeDrawer();
                    },
                    title: Text(_searchResults[i].name),
                  );
                },
                itemCount: _searchResults.length,
              )
            :
            // show something if the search returned no results
            const Icon(
              Icons.search_off,
              color: Colors.black,
              size: 100.0,
            ),
          ),
        ],
      ),
    ),
    ...
  );
}
```

Now that we have implemented the `Drawer` UI. Now we can add its activation to the search query method. We do this by putting the locations returned from our search into the `_searchResults` variable and then opening the `Drawer` from our `Scaffold`.

```dart
void search(String value) {
  ...
  // fetch all (max 30) locations that match the query
  getLocationsByQuery(query: query, filter: filter).then((locations) {
    if (locations != null && locations.isNotEmpty) {
      // show search results drawer
      setState(() {
        _searchResults = locations;
        _scaffoldKey.currentState?.openDrawer();
      });
      ...
    }
  }).catchError((err) {
    ...
  });
}
```

See the full example of the search method here: [main.dart](https://github.com/MapsPeople/getting\_started\_flutter/blob/main/lib/main.dart#L133-L165)

### Filter Locations on Map Based on Search Results[​](https://docs.mapsindoors.com/getting-started/flutter/search#filter-locations-on-map-based-on-search-results) <a href="#filter-locations-on-map-based-on-search-results" id="filter-locations-on-map-based-on-search-results"></a>

When getting a search result, you might want to only show those search results on the map. You can do this through [calling `setFilterWithLocations` on `MapsIndoorsWidget`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget/setFilterWithLocations.html). This method has different parameters to make it easier for you as a developer to fit your exact need in terms of animation and more.

The standard implementation animates the camera to fit all Locations on the map and show the info window of a Location, if it's a list of only one Location.

When you are done showing the search results you can [call `clearFliter`](https://pub.dev/documentation/mapsindoors\_googlemaps/latest/mapsindoors/MapsIndoorsWidget/clearFilter.html).

```dart
  void search(String value) {
    // we should clear the search filter when the query is empty
    if (value.isEmpty) {
      _mapControl.clearFilter();
      setState(() {
        _searchResults = [];
      });
    }
    ...
    getLocationsByQuery(query: query, filter: filter).then((locations) {
      if (locations != null && locations.isNotEmpty) {
        ...
        // filter the map to only show matches
        _mapControl.setFilterWithLocations(locations, MPFilterBehavior.DEFAULT);
      }
    }).catchError((err) {
      ...
    });
  }
```

Expected result:

<figure><img src="../../../.gitbook/assets/flutter_search.gif" alt=""><figcaption></figcaption></figure>

The accompanying UI and implementation of this search experience can be found in the getting started app sample. [Getting Started App sample](https://github.com/MapsPeople/getting\_started\_flutter)

[\
](https://docs.mapsindoors.com/getting-started/flutter/map)
