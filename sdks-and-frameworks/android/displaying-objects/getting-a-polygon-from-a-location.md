# Getting a Polygon from a Location

Some locations in MapsIndoors can have additional polygon information. These polygons can be used to render a room or area in a special way or make geofences, calculating whether another point or location is contained within the polygon. If a `MPLocation` has polygons, these can be retrieved using:

```kotlin
// MPGeometry is a sealed interface, so the `when` covers every geometry a location can have
when (val geometry = location.geometry) {
    is MPPoint -> {
        val lat = geometry.lat
        val lng = geometry.lng
    }
    is MPPolygonGeometry -> {
        // Get all the paths in the polygon
        val paths: List<List<MPLatLng>> = geometry.gmsPath
        // Outer ring (first)
        for (coordinate in paths.first()) {
            val lat = coordinate.lat
            val lng = coordinate.lng
        }
        // Optional: Inner rings (holes)
        for (hole in paths.drop(1)) {
            for (coordinate in hole) {
                val lat = coordinate.lat
                val lng = coordinate.lng
            }
        }
    }
    is MPMultiPolygonGeometry -> {
        // Each polygon has an outer ring and optional holes, like above
        for (polygon in geometry.toMPPolygonGeometries()) {
            val paths: List<List<MPLatLng>> = polygon.gmsPath
        }
    }
    null -> {
        // The location has no geometry
    }
}
```

As demonstrated above, a polygon's outer ring/path as well as holes are lists of `MPLatLng` coordinates. Not all locations have polygons: some are points. On the contrary, some locations, like entire building floors, might have more than one polygon, in which case the geometry is an `MPMultiPolygonGeometry`.
