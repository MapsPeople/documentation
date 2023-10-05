# Creating a Search Experience



{% tabs %}
{% tab title="iOS v4" %}
This is an example of creating a simple search experience using MapsIndoors. We will create a map with a search button which leads to another view controller that handles the search and selection. Select a Location to go back to the map and show the selected Location on the map.

We will start by creating a simple search controller that handles search and selection of MapsIndoors Locations.

Declare a protocol for our Location selection with a `didSelectLocation` method

```
protocol MySearchControllerDelegate {
    func didSelectLocation(location: MPLocation)
}
```

Define `MySearchController`. In this tutorial our search controller is a `UIViewController` that implements the protocols `UISearchBarDelegate`, `UITableViewDelegate` and `UITableViewDataSource`.

```
class MySearchController: UIViewController, UISearchBarDelegate, UITableViewDelegate, UITableViewDataSource {
```

Setup member variables for `MySearchController`:

* An instance of type `MPQuery`
* An array of `MPLocation` to hold your list of results
* Your delegate object
* A search bar view
* A table view

```
let query = MPQuery()
var locations = [MPLocation]()
var delegate: MySearchControllerDelegate? = nil
let tableView = UITableView()
let searchBar = UISearchBar()
```

In `viewDidLoad`, wire up your view controller to the table view and search bar.

```
searchBar.delegate = self
tableView.delegate = self
tableView.dataSource = self
```

Register a class for the reusable table view cell.

```
tableView.register(UITableViewCell.self, forCellReuseIdentifier: "reuseIdentifier")
```

Arrange the search bar and the table view in a stack view.

```
let topFiller = UIView()
let stackView = UIStackView(arrangedSubviews: [topFiller, searchBar, tableView])
stackView.axis = .vertical
view = stackView
let kw = UIApplication.shared.keyWindow
topFiller.heightAnchor.constraint(equalToConstant:kw?.safeAreaInsets.top ?? 0).isActive = true
topFiller.backgroundColor = .blue
searchBar.barTintColor = .blue
searchBar.tintColor = .white
searchBar.showsCancelButton = true
searchBar.becomeFirstResponder()
```

In `MySearchController`, implement the `numberOfSections` method, return 1.

```
func numberOfSections(in tableView: UITableView) -> Int {
    return 1
}
```

Implement the `numberOfRowsInSection` method, return the length of your locations array.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return locations.count
}
```

Implement the `textDidChange` method:

* Change the query objects query property to reflect the current search text
* Call [`MPMapsIndoors.shared.locationsWith(query:filter:)`](https://app.mapsindoors.com/mapsindoors/reference/ios/4.1.3/documentation/mapsindoors/mapsindoorsshared/locationswith\(query:filter:\)) with the modified query
* Reload table view

```
func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
    if searchText.count > 0 {
        query.query = searchText
        let filter = MPFilter()
        filter.take = 10

        Task {
            locations = await MPMapsIndoors.shared.locationsWith(query: query, filter: filter)
            self.tableView.reloadData()
            self.delegate?.didShowLocations(locations: locations)
        }
    } else {
        locations = []
        self.tableView.reloadData()
        self.delegate?.didShowLocations(locations: locations)
    }
}
```

Implement the `searchBarCancelButtonClicked` method, with dismissal of the view controller.

```
func searchBarCancelButtonClicked(_ searchBar: UISearchBar) {
    self.dismiss(animated: true, completion: nil)
}
```

Implement the `tableView:cellForRowAt` method. Set the `cell.textLabel.text` to reflect the _name_ of the location of same index.

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "reuseIdentifier", for: indexPath)
    cell.textLabel?.text = locations[indexPath.row].name
    let defaultValue = ""
    cell.textLabel?.text?.append(", \(locations[indexPath.row].roomId ?? defaultValue), \(locations[indexPath.row].building ?? defaultValue), \(locations[indexPath.row].venue ?? defaultValue)")
    return cell
}
```

Implement the `tableView:didSelectRowAt` method. In this example we call the delegate method and dismiss the view controller. Delegate method will be handled by SearchMapController.

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    delegate?.didSelectLocation(location: locations[indexPath.row])
    self.dismiss(animated: true, completion: nil)
}
```

[See the sample in MySearchController.swift](https://github.com/MapsIndoors/MapsIndoorsIOS/blob/master/Example/DemoSamples/Search/MySearchController.swift)
{% endtab %}

{% tab title="iOS v3" %}
This is an example of creating a simple search experience using MapsIndoors. We will create a map with a search button which leads to another view controller that handles the search and selection. Select a Location to go back to the map and show the selected Location on the map.

We will start by creating a simple search controller that handles search and selection of MapsIndoors Locations.

Declare a protocol for our Location selection with a `didSelectLocation` method

```
protocol MySearchControllerDelegate {
    func didSelectLocation(location:MPLocation)
}
```

Define `MySearchController`. In this tutorial our search controller is a `UIViewController` that implements the protocols `UISearchBarDelegate`, `UITableViewDelegate` and `UITableViewDataSource`.

```
class MySearchController: UIViewController, UISearchBarDelegate, UITableViewDelegate, UITableViewDataSource {
```

Setup member variables for `MySearchController`:

* An instance of type `MPLocationService`
* An instance of type `MPQuery`
* An array of `MPLocation` to hold your list of results
* Your delegate object
* A search bar view
* A table view

```
let locationService = MPLocationService.sharedInstance()
let query = MPQuery()
var locations:[MPLocation] = []
var delegate:MySearchControllerDelegate? = nil
let tableView = UITableView()
let searchBar = UISearchBar()
```

In `viewDidLoad`, wire up your view controller to the table view and search bar.

```
searchBar.delegate = self
tableView.delegate = self
tableView.dataSource = self
```

Register a class for the reusable table view cell.

```
tableView.register(UITableViewCell.self, forCellReuseIdentifier: "reuseIdentifier")
```

Arrange the search bar and the table view in a stack view.

```
let topFiller = UIView()
let stackView = UIStackView(arrangedSubviews: [topFiller, searchBar, tableView])
stackView.axis = .vertical
view = stackView
let kw = UIApplication.shared.keyWindow
topFiller.heightAnchor.constraint(equalToConstant:kw?.safeAreaInsets.top ?? 0).isActive = true
topFiller.backgroundColor = .blue
searchBar.barTintColor = .blue
searchBar.tintColor = .white
searchBar.showsCancelButton = true
searchBar.becomeFirstResponder()
```

In `MySearchController`, implement the `numberOfSections` method, return 1.

```
func numberOfSections(in tableView: UITableView) -> Int {
    return 1
}
```

Implement the `numberOfRowsInSection` method, return the length of your locations array.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return locations.count
}
```

Implement the `textDidChange` method:

* Change the query objects query property to reflect the current search text
* Call `getLocationsUsing` with the modified query
* In the callback block, reset the locations array with new results
* Reload table view

```
func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
    if searchText.count > 0 {
        query.query = searchText
        let filter = MPFilter()
        filter.take = 10
        locationService.getLocationsUsing(query, filter: filter) { (locations, error) in
            if error == nil {
                self.locations = locations!
                self.tableView.reloadData()
            }
        }
    } else {
        self.locations = []
        self.tableView.reloadData()
    }
}
```

Implement the `searchBarCancelButtonClicked` method, with dismissal of the view controller.

```
func searchBarCancelButtonClicked(_ searchBar: UISearchBar) {
    self.dismiss(animated: true, completion: nil)
}
```

Implement the `tableView:cellForRowAt` method. Set the `cell.textLabel.text` to reflect the _name_ of the location of same index.

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "reuseIdentifier", for: indexPath)
    cell.textLabel?.text = locations[indexPath.row].name
    let defaultValue = ""
    cell.textLabel?.text?.append(", \(locations[indexPath.row].roomId ?? defaultValue), \(locations[indexPath.row].building ?? defaultValue), \(locations[indexPath.row].venue ?? defaultValue)")
    return cell
}
```

Implement the `tableView:didSelectRowAt` method. In this example we call the delegate method and dismiss the view controller. Delegate method will be handled by SearchMapController.

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    delegate?.didSelectLocation(location: locations[indexPath.row])
    self.dismiss(animated: true, completion: nil)
}
```

[See the sample in MySearchController.swift](https://github.com/MapsIndoors/MapsIndoorsIOS/blob/master/Example/DemoSamples/Search/MySearchController.swift)
{% endtab %}
{% endtabs %}
