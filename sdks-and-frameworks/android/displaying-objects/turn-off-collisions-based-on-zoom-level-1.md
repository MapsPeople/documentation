# Turn Off Collisions Based on Zoom Level

When using the MapsIndoors SDK, the system for detecting collisions will sometimes, at high zoom levels, result in the Labels of POI's that are close together being hidden, no matter what you do. Here we present a small workaround, so you can disable collisions for specific zoom levels.

For Android, there are two ways of implementing this, and which you should use depends on your desired map behavior.

#### Google Maps for Android[​](https://docs.mapsindoors.com/turn-off-collisions-based-on-zoom#google-maps-for-android) <a href="#google-maps-for-android" id="google-maps-for-android"></a>

If you wish for the collision behavior to change when the maps stops moving, you should use this piece of code. This would generally be the most performance-friendly option.

```
val maxZoomForCollisions = 20 //set your desired zoom level upon which the collision behaviour changes

mGoogleMap.setOnCameraIdleListener {
    if (mGoogleMap.cameraPosition.zoom >= maxZoomForCollisions) {
        MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.ALLOW_OVERLAP)
    } else {
        MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.REMOVE_LABEL_FIRST)
    }
}
```

However, if you wish for the collision behavior to change when the maps starts moving instead, you should use this.

```
val maxZoomForCollisions = 20 //set your desired zoom level upon which the collision behaviour changes

mGoogleMap.setOnCameraMoveListener {
    if (mGoogleMap.cameraPosition.zoom >= maxZoomForCollisions) {
        MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.ALLOW_OVERLAP)
    } else {
        MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.REMOVE_LABEL_FIRST)
    }
}
```

#### Mapbox for Android[​](https://docs.mapsindoors.com/turn-off-collisions-based-on-zoom#mapbox-for-android) <a href="#mapbox-for-android" id="mapbox-for-android"></a>

The code for Mapbox is somewhat different - Here you must make an `onMoveListener`, and insert the implementation into the relevant section - `onMove`, `onMoveBegin` or `onMoveEnd`. Generally, `onMoveEnd` would be recommended, and will be shown below, as it is the most performance-friendly, but code may be moved into the others, if your specific functionality can be achieved through this.

```
val maxZoomForCollisions = 20

mMapBoxMap?.addOnMoveListener(object : OnMoveListener {
    override fun onMove(detector: MoveGestureDetector): Boolean {
      // insert implementation here if desired
      return false
    }

    override fun onMoveBegin(detector: MoveGestureDetector) {
      // insert implementation here if desired
    }

    override fun onMoveEnd(detector: MoveGestureDetector) {
        // the implementation starts here
        if (mMapBoxMap?.cameraState?.zoom!! >= maxZoomForCollisions) {
            MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.ALLOW_OVERLAP)
        } else {
            MapsIndoors.getSolution()?.config?.setCollisionHandling(MPCollisionHandling.REMOVE_LABEL_FIRST)
        }
        // the implementation ends here
    }

})
```

[\
](https://docs.mapsindoors.com/location-details)
