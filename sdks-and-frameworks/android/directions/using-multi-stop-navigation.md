---
description: >-
  Multi-stop navigation has been introduced with the release of 4.8.0. This
  allows users to be navigated to multiple stops within a single route.
---

# Using multi-stop navigation

### Querying a multi-stop route

To query a multistop route a new overload `MPDirectionsService.query` has been introduced `MPDirectionsService.query(from: MPPoint, to: MPPoint, stops: List<MPPoint>?, optimize: Boolean)`.

<pre class="language-kotlin"><code class="lang-kotlin">private var directionsService: MPDirectionsService = MPDirectionsService()

//Example of querying an optimized route with multiple stops
fun getRoute() {
    if (directionsService != null) {
        directionsService = MPDirectionsService()
<strong>    }
</strong><strong>    //Setting listener to receive the queried route
</strong><strong>    directionsService.setRouteResultListener { route, error ->
</strong><strong>        if (error == null &#x26;&#x26; route != null) {
</strong>            //Route is received
        } else {
            Log.i("Directions", "Error: $error")
        }
<strong>    }
</strong><strong>    //Creating variables to use for the query
</strong><strong>    val origin = MPPoint(57.05800975, 9.949916517)
</strong>    val destination = MPPoint(57.058278, 9.9512196, 10.0)
    val stops = listOf(MPPoint(57.0582701, 9.9508396, 0.0), MPPoint(57.0580431, 9.9505475, 0.0), MPPoint(57.0580843, 9.9506085, 10.0))
    
    directionsService.query(origin, destination, stops, true)
}
</code></pre>

### Showing and configuring a multi-stop route on the map

With the introduction of the multi-stop routes. There has also been added new functionality to the `MPDirectionsRenderer` to facilitate the new multi-stop routes.

You can still render the route as a Route with stops, using `setRoute(route: MPRoute)` on the DirectionsRenderer. But the interface of the `MPDirectionsRenderer` has been expanded with a `defaultStopIcon` as well as an overload of: `setRoute(route: MPRoute, icons: HashMap<Integer, MPRouteStopIconProvider>?)`&#x20;

<figure><img src="../../../.gitbook/assets/Screenshot_20240521-141733.png" alt="" width="375"><figcaption><p>By default route stops will be shown as a red pin with numbers</p></figcaption></figure>

The `defaultStopIcon` can be configured to show any image. It can also be set to null, to not show any icon for the stops on the Route. We supply the `MPRouteStopIconConfig` that allows you to customize the default pin to fit your application.&#x20;

```kotlin
//Changing the default icon to be blue, with a Meeting Room label and with no number inside the pin
val defaultRouteStopIcon = MPRouteStopIconConfig.Builder(myContext)
                                .setColor(Color.BLUE)
                                .setNumbered(false)
                                .setLabel("Meeting Room")
                                .build()
directionsRenderer?.setDefaultRouteStopIconConfig(defaultRouteStopIcon)
```



<figure><img src="../../../.gitbook/assets/Screenshot_20240521-140958.png" alt="" width="375"><figcaption><p>Customized MPRouteStopIcon</p></figcaption></figure>

To use your own images, you can extend the MPRouteStopIconProvider with your own class. Here is an example using a bitmap for the image

```kotlin
class BitmapRouteStopIcon(val image: Bitmap) : MPRouteStopIconProvider() {
    override fun getImage(): Bitmap? {
        return image
    }
}
```

#### Adding a custom image to a specific waypoint

It is also possible to render an image specific to a single stop. By using `setRoute(route: MPRoute, icons: HashMap<Integer, MPRouteStopIconProvider>?)`

```kotlin
val stopIcons = mapOf(2 to MPRouteStopIconConfig.Builder(this).setColor(Color.BLUE).build())
directionsRenderer?.setRoute(route, HashMap(stopIcons))
```

Here we will show a blue icon for the third stop. Leaving the first two indexes as null, means that the renderer will use the `defaultStopIcon`. If you want to render a specific known stop on an optimized route. You can find the ordering of the stops on an optimized route through `MPRoute.orderedStopIndexes`.
