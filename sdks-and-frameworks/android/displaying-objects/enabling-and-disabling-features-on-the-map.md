---
description: >-
  With the Release of 4.5.0 on mapbox-v11 it is possible to hide certain map
  features on the map
---

# Enabling and Disabling features on the map

#### Toggling MapsIndoors features

You can toggle the rendered features on the map through MapControl, by using `hiddenFeatures`

This code example removes all 3D features on the map.

```kotlin
mapControl.hiddenFeatures = listOf(MPFeatureType.EXTRUDED_BUILDINGS,
                                             MPFeatureType.EXTRUSION_3D,
                                             MPFeatureType.WALLS_3D,
                                             MPFeatureType.MODEL_3D)
```

All features that can be enabled or disabled can be found on [`MPFeatureType`](https://app.mapsindoors.com/mapsindoors/reference/android/4.8.8/MapsIndoorsSDK/com.mapsindoors.core/-m-p-feature-type/index.html)

Clearing the hiddenFeatures can be done by setting it to null.

```kotlin
mapControl.hiddenFeatures = null
```
