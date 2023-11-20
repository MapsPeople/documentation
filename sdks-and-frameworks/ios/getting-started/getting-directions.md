# Getting Directions

### Getting Directions to a Location[​](https://docs.mapsindoors.com/getting-started/ios/v4/directions#getting-directions-to-a-location) <a href="#getting-directions-to-a-location" id="getting-directions-to-a-location"></a>

Examples

See the full example of Directions here: [BasicDirection.swift](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples/blob/main/MapsIndoorsSDK-iOS-Examples/Getting%20Started/BasicDirection.swift)

In a lot of cases, the user might not want to display a specific region, but rather get a route proposed that will lead them to where they need to go. In order to accomplish this, we utilize the [MPDirectionsService](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpdirectionsservice) class, which we will be able to query through the [MPDirectionsQuery](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpdirectionsquery) class, while to actually display the result we use the [MPDirectionsRenderer](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpdirectionsrenderer).

First things first, let us add in some new variables, namely the renderer and two points we wish to acquire directional data between (the origin and destination). Usually, the origin point would refer to the location of the user, however for the purposes of demonstration we will use a static origin point.

```swift
var directionsRenderer: MPDirectionsRenderer?
var origin: MPLocation?
```

In order to update the destination variable we simply employ the same concept as we did when adding the search bar, namely we utilise the `func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)` function. In this case, we simply need to update our function to store the point of the selected location and call `directions()`, which is a function we will add in a bit that will handle the construction of the directions query and rendering.

```swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        guard let location = searchResult?[indexPath.row] else { return }
        mpMapControl?.goTo(entity: location) // Use a retained MPMapControl object
        tableView.removeFromSuperview()
        
        // Call the directions(to:) function
        directions(to: location)
  }
```

We can now add in the `directions()` function,

```swift
func directions(to destination: MPLocation) {
        guard let mapControl = mpMapControl else { return }
        
        if directionsRenderer == nil {
            directionsRenderer = mapControl.newDirectionsRenderer()
        }
        
        let directionsQuery = MPDirectionsQuery(origin: origin!, destination: destination)
        
        Task {
            do {
                let route = try await MPMapsIndoors.shared.directionsService.routingWith(query: directionsQuery)
                directionsRenderer?.route = route
                directionsRenderer?.routeLegIndex = 0
                directionsRenderer?.animate(duration: 5)
            } catch {
                print("Error getting directions: \(error.localizedDescription)")
            }
        }
}
```

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/v4/directions#expected-result) <a href="#expected-result" id="expected-result"></a>

<figure><img src="../../../.gitbook/assets/ios_directions.gif" alt=""><figcaption></figcaption></figure>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_directions.gif)
