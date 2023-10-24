# Getting a Polygon from a Location

Some locations in MapsIndoors can have additional polygon information. These polygons can be used to render a room or area in a special way or make geofences, calculating whether another point or location is contained within the polygon.

If a `MPLocation` has polygons, these can be retrieved using:

```swift
let polygons = location.geometry
let polygon = polygons.first
switch location.geometry?.type {
case "Point":
    let point = location.geometry as? MPPoint
case "Polygon":
    if let polygon = location.geometry as? MPPolygonGeometry, let perimeter = polygon.path {
        for point in perimeter {
            let lat = point.coordinate.latitude
            let long = point.coordinate.longitude
        }
    }

case "MultiPolygon":
    if let multiPolygon = location.geometry as? MPMultiPolygonGeometry {
        // ...
    }
default:
    print("Unsupported geometry")
}
```

As demonstrated above, a polygon's outer ring/path as well as holes are arranged as `[longitude, latitude]` pairs. As not all locations have polygons, the polygon array may be empty. On the contrary, some locations, like entire building floors, might have more than polygon.
