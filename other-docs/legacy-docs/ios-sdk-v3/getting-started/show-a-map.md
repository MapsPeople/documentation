# Display a Map

Now that we have the prerequisite API keys, and the project set up, we can start adding basic functionality to the app. We will start by having the app display a map.

### Display a Map with MapsIndoors <a href="#display-a-map-with-mapsindoors" id="display-a-map-with-mapsindoors"></a>

{% hint style="info" %}
Please note that data in MapsIndoors is loaded asynchronously. This results in behavior where data might not have finished loading if you call methods accessing it immediately after initializing MapsIndoors. Best practice is to set up `listeners` or `delegates` to inform of when data is ready. Please be aware of this when developing using the MapsIndoors SDK.
{% endhint %}

In order to accomplish this we will be utilising the [MPMapControl Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_map\_control.html). Open the file, `ViewController.swift` and, once again, add in the following import statements to the top of the file,

```
import GoogleMaps  
import MapsIndoors
```

Furthermore, within the ViewController class, add in a map controller variable as such,

```
class ViewController: UIViewController {
private var mapControl:MPMapControl?
var mapView = GMSMapView.map(withFrame: CGRect.init(x: 0, y: 0, width: UIScreen.main.bounds.width, height: UIScreen.main.bounds.height), camera: GMSCameraPosition())
...
}
```

The [MPMapControl Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_map\_control.html) the is connective class between the Google Map and the MapsIndoors. It allows the two services to collaborate by overlaying the MapsIndoors information over the Google Maps information.

_Note: This also means logic is appended onto many Google Maps listeners, which means that using Google Maps listeners directly might break intended behavior of the MapsIndoors experience. We recommend checking our_ [_reference docs_](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/index.html)_, and see if you can add a specific `Listener` through the `MapControl`. If this is possible, it is highly recommend you do so._

What this means is that we should first create our Google Map, in order to do this we add the following code to our `viewDidLoad()` method,

```
self.view.addSubview(mapView)
```

Running the app like this does indeed display a map, however this is through the use of Google Maps exclusively and with a default map region displayed. Let us try and add in MapsIndoors and showcase a specific location. To accomplish this we add in following after the previously inserted code,

```
self.mapControl = MPMapControl(map: mapView)  
let query = MPQuery()  
let filter = MPFilter()  
query.query = "White House"  
filter.take = 1  
MPLocationService.sharedInstance().getLocationsUsing(query, filter: filter) { (locations, error) in  
    if let location = locations?.first {  
        self.mapControl?.go(to:location)  
    }  
}
```

We have now added a _very_ simple search feature! Running the app now should yield a combined map of The White House, showing both the external and internal geographical information. However, let us try and understand what is actually happening.

We initialise the mapControl for MapsIndoors with respect to the `mapView` generated by the Google Maps API.

Afterwards, we set-up a Query and Filter based on the [MPQuery Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_query.html#a9da8ff62b6d17e45403b9005c73e811f) and [MPFilter Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_filter.html) classes respectively, these serve as our search specifications.

Finally, the [MPLocationService Class](https://app.mapsindoors.com/mapsindoors/reference/ios/v3/interface\_m\_p\_location\_service.html#a140844121a239dcf1709210e1723c312) enables us to search through the full collection of Locations based on our previously established query and filter, such that we can go to that location on the map. Moving the location is all handled by mapControl as it handles it for both Google Maps and MapIndoors.

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/display-a-map#expected-result) <a href="#expected-result" id="expected-result"></a>

![An animation showing the desired behaviour of this tutorial](https://docs.mapsindoors.com/img/getting-started/ios\_display-a-map.png)

Feel free to change the query from "White House" to a known building in _your_ MapsIndoors dataset.

We have now added a search feature to the app, albeit a search feature which the user cannot interact with. Next, let us look into how we can let the user interact with the map through a search bar.