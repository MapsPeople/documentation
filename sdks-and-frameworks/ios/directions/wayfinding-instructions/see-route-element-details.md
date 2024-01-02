---
description: iOS v4
---

# See Route Element Details

In this tutorial we will request a route and list the route parts. A MapsIndoors route is made of one or more legs, each containing one or more steps.

Start by creating a `UITableViewController` implementation with a `MPRoute` property.

```swift
class ShowRouteController: UITableViewController {
    var route: MPRoute?
```

Inside the `viewDidLoad` function, setup a directions service, call the directions service and save the route result to your `route` property

```swift
let origin = MPPoint(latitude: 57.057917, longitude: 9.950361, z: 0)!
let destination = MPPoint(latitude: 57.058038, longitude: 9.950509, z: 1)!
let directionsQuery = MPDirectionsQuery(originPoint: origin, destination: destination)

route = try await MPMapsIndoors.shared.directionsService.routingWith(query: directionsQuery)
```

Override the `numberOfRowsInSection` function to return the number of steps in the current leg plus the leg itself

```swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    if let steps = route?.legs[1].steps {
        return steps.count + 1
    }
    return 0
}
```

Override the `numberOfSections` function to return the number of legs

```swift
override func numberOfSections(in tableView: UITableView) -> Int {
    if let legs = route?.legs {
        return legs.count
    }
    return 0
}
```

Override the `titleForHeaderInSection` function to return the leg index

```swift
override func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
    return "Route Leg \(section)"
}
```

Override the `tableView:cellForRowAt` function to return leg index and step index if applicable

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    if indexPath.row > 0 {
        cell.textLabel?.text = "Show leg \(indexPath.section), step \(indexPath.row - 1)"
    } else {
        cell.textLabel?.text = "Show leg \(indexPath.section), all steps"
    }
    return cell
}
```
