# Directions

### Getting Directions to a Location[​](https://docs.mapsindoors.com/getting-started/ios/directions#getting-directions-to-a-location) <a href="#getting-directions-to-a-location" id="getting-directions-to-a-location"></a>

In a lot of cases, the user might not want to display a specific region, but rather get a route proposed that will lead them to where they need to go. In order to accomplish this, we utilize the [MPDirectionsService](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_directions\_service.html) class, which we will be able to query through the [MPDirectionsQuery](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_directions\_query.html) class, while to actually display the result we use the [MPDirectionsRenderer](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_directions\_renderer.html).

First things first, let us add in some new variables, namely the renderer and two points we wish to acquire directional data between (the origin and destination). Usually, the origin point would refer to the location of the user, however for the purposes of demonstration we will use a static origin point.

```
var origin:MPPoint = MPPoint(lat: 38.897382, lon: -77.037447, zValue:0)
var destination:MPPoint = MPPoint.init()
private let renderer = MPDirectionsRenderer()
```

In order to update the destination variable we simply employ the same concept as we did when adding the search bar, namely we utilise the `func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)` function. In this case, we simply need to update our function to store the point of the selected location and call `directions()`, which is a function we will add in a bit that will handle the construction of the directions query and rendering.

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let location:MPLocation = searchResult![indexPath.row]
        self.destination = location.getPoint()!
        self.mapControl?.go(to: location)
        tableView.removeFromSuperview()
        directions()
    }
```

We can now add in the `directions()` function,

```
func directions() {
        let directions = MPDirectionsService()
        let directionsQuery = MPDirectionsQuery(originPoint: origin, destination: destination)

        directions.routing(with: directionsQuery) { (route, error) in
          self.renderer.map = self.mapView
          self.renderer.route = route
          self.renderer.routeLegIndex = 0
          self.renderer.animate(5)
        }
      }
```

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/directions#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_directions.gif)
