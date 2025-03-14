# Search

### Search for a Location[​](https://docs.mapsindoors.com/getting-started/ios/v4/search#search-for-a-location) <a href="#search-for-a-location" id="search-for-a-location"></a>

From here onwards, code for both Mapbox and Google Map`s` is similar.

Take a look at the following code. As discussed before, this will select a location specifically named "The Crow".

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController.swift#L28-L35" %}

Our goal now is to enable the user to interact with a search bar and move the map with respect to their search. Therefore, we need to implement a bit more functionality in our ViewController class, so feel free to update it as follows.

```swift
class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UISearchBarDelegate {
...
}
```

Let us start off by implementing the search bar. In this case, we add the following variables to our `ViewController` class. The tableView will be used to allow the user to see and interact with the search results.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController.swift#L86-L87" %}

And let us also make sure the search bar is correctly displayed in our view and the `ViewController` is its delegate.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ExampleUI.swift#L19-L31" %}

Finally, let us add the functions necessary for our class to conform to the `UISearchBarDelegate` and  `UITableViewDataSource` protocols for the actual search button functionality.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController%2BUISearchBarDelegate.swift" %}

This responds to what the user enters in the search bar and tells MapsIndoors to search for the entered text and show the found Locations in the table view. This is almost exactly the same as the initial simple search we included in [Display a Map](https://docs.mapsindoors.com/getting-started/ios/display-a-map/) with MapsIndoors.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController%2BUITableViewDataSource.swift" %}

These three functions simply outline the appearance of the table. Namely, how many rows to show and which text to represent each entry.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController%2BUITableViewDelegate.swift" %}

This instructs what to do once an item is selected – in this case we simply go to the specified Location and hide the table view again.

At this point we should have a functional map with a search feature.

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/v4/search#expected-result) <a href="#expected-result" id="expected-result"></a>

<figure><img src="../../../.gitbook/assets/iOS Search result.gif" alt=""><figcaption></figcaption></figure>
