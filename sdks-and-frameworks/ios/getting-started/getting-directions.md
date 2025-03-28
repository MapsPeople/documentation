# Getting Directions

### Getting Directions to a Location[​](https://docs.mapsindoors.com/getting-started/ios/v4/directions#getting-directions-to-a-location) <a href="#getting-directions-to-a-location" id="getting-directions-to-a-location"></a>

See an example of Directions here: [BasicDirection.swift](https://github.com/MapsPeople/MapsIndoorsSDK-iOS-Examples/blob/main/MapsIndoorsSDK-iOS-Examples/Getting%20Started/BasicDirection.swift)

In a lot of cases, the user might not want to display a specific region, but rather get a route proposed that will lead them to where they need to go. In order to accomplish this, we utilize the [MPDirectionsService](https://app.mapsindoors.com/mapsindoors/reference/ios/4.9.5/documentation/mapsindoors/mpdirectionsservice) class, which we will be able to query through the [MPDirectionsQuery](https://app.mapsindoors.com/mapsindoors/reference/ios/4.9.5/documentation/mapsindoors/mpdirectionsrenderer) class, while to actually display the result we use the [MPDirectionsRenderer](https://app.mapsindoors.com/mapsindoors/reference/ios/v4-doc/documentation/mapsindoors/mpdirectionsrenderer).

First things first, let us add in some new variables, namely the renderer and two points we wish to acquire directional data between (the origin and destination). Usually, the origin point would refer to the location of the user, however for the purposes of demonstration we will use a preset origin point (the Location found in [show-a-map.md](show-a-map.md "mention")).

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController.swift#L70-L74" %}

In order to update the destination variable we simply employ the same concept as we did when adding the search bar, namely we utilise the `func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)` function. In this case, we simply need to update our function to store the point of the selected location and call `directions()`, which is a function we will add in a bit that will handle the construction of the directions query and rendering.

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController%2BUITableViewDelegate.swift" %}

We can now add in the `directions()` function:

{% @github-files/github-code-block url="https://github.com/MapsPeople/MapsIndoorsGettingStarted-Mapbox/blob/12aaf3c15d8516842e76effddec95416f3a7e3c4/MapsIndoorsGettingStarted-Mapbox/ViewController.swift#L54-L68" %}

### Expected Result[​](https://docs.mapsindoors.com/getting-started/ios/v4/directions#expected-result) <a href="#expected-result" id="expected-result"></a>

<figure><img src="../../../.gitbook/assets/iOS Directions.gif" alt=""><figcaption></figcaption></figure>
