# Getting a Polygon from a Location

Some locations in MapsIndoors can have additional polygon information. These polygons can be used to render a room or area in a special way or make geofences, calculating whether another point or location is contained within the polygon.

If a `MPLocation` has polygons, these can be retrieved using the `MPGeometryHelper` class.

```swift
let polygons = MPGeometryHelper.polygonsForLocation(location : MPLocation)
let polygon = polygons.first
let path = polygon.path
let holes = polygon.holes
for coordinate in path {
    let lat = coordinate[1]
    let lng = coordinate[0]
}
```

As demonstrated above, a polygon's outer ring/path as well as holes are arranged as `[longitude, latitude]` pairs. As not all locations have polygons, the polygon array may be empty. On the contrary, some locations, like entire building floors, might have more than polygon.
