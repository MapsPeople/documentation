# Enabling and Disabling features on the map

From version 4.3.9 of MapsIndoorsMapbox11 it is possible to hide certain map features on the map.

### **Toggling MapsIndoors features**

You can toggle the rendered features on the map through MapControl, by using `hiddenFeatures`.

This code example removes all 3D features on the map.

```swift
mapControl?.hiddenFeatures = [MPFeatureType.extrudedBuildings.rawValue,
                              MPFeatureType.extrusion3D.rawValue,
                              MPFeatureType.walls3D.rawValue,
                              MPFeatureType.model3D.rawValue]
```

All features that you can enable and disable can be seen on [`MPFeatureType`](https://app.mapsindoors.com/mapsindoors/reference/android/4.8.8/MapsIndoorsSDK/com.mapsindoors.core/-m-p-feature-type/index.html)​.

Clearing the hiddenFeatures can be done by setting it to an empty array:

```swift
mapControl?.hiddenFeatures = []
```
