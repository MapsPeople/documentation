# Search

### Search for a Location[​](https://docs.mapsindoors.com/getting-started/ios/search#search-for-a-location) <a href="#search-for-a-location" id="search-for-a-location"></a>

Our goal now is to enable the user to interact with a search bar and move the map with respect to their search. Therefore, we need to implement a bit more functionality into our ViewController class, so feel free to update it as followed.

```
class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UISearchBarDelegate {
...
}
```

Let us start off by implementing the search bar. In this case, we add the following variables to our class. The vertical offset of the search bar here is simply to avoid the search bar from colliding with the navigation bar. The tableView will be used to allow the user to see and interact with the search results.

```
var searchResult:[MPLocation]?
lazy var destinationSearch:UISearchBar = UISearchBar.init(frame: CGRect.init(x: 0, y: 40, width: 0, height: 0))
var tableView = UITableView.init(frame: CGRect.init(x: 0, y: 90, width: UIScreen.main.bounds.width, height: UIScreen.main.bounds.height))
```

And let us also make sure the search bar delegated and correctly displayed in our view.

```
override func viewDidLoad(){
destinationSearch.sizeToFit()
self.destinationSearch.delegate = self
self.view.addSubview(destinationSearch)
tableView.dataSource = self
tableView.delegate = self
...
}
```

Finally, let us add the functions neccessary for our class to conform to the UITableViewDataSource protocol and the actual search button functionality.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return self.searchResult?.count ?? 0
}

func numberOfSections(in tableView: UITableView) -> Int {
    return 1
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell.init()
    let location = self.searchResult?[indexPath.row]
    cell.textLabel?.text = location?.name ?? ""
    return cell
}

func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    let location:MPLocation = searchResult![indexPath.row]
    self.mapControl?.go(to: location)
    tableView.removeFromSuperview()
}

func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
    self.view.addSubview(tableView)
    let query = MPQuery.init()
    let filter = MPFilter.init()
    query.query = searchText
    filter.take = 100
    MPLocationService.sharedInstance().getLocationsUsing(query, filter: filter) { (locations, error) in
            self.searchResult = locations
            self.tableView.reloadData()
    }
}
```

Here, the first 3 functions simply outline the appearence of the table. Namely, how many rows to show and which text to represent each entry.

The main functions of note in this case are, `func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)` and `func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String)`.

The former denotes what to do once an item is selected, in this case we simply go to the specified location and hide the table view again, while the latter functions almost exactly the same as the initial simple search we included in [Display a Map](https://docs.mapsindoors.com/getting-started/ios/display-a-map/) with MapsIndoors.

At this point we should have a functional map with a search feature.

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/search#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_search.gif)[\
](https://docs.mapsindoors.com/getting-started/ios/display-a-map)
