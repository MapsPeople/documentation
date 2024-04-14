---
description: >-
  This documentation refers to the change of the Mapbox engine in 4.5.0
---

# Migrating to Mapbox V11

With the release of Mapbox V11, we are migrating the V4 SDK to use Mapbox V11 to make use of the new features offered by Mapbox. As this version bump can include breaking changes for your implementation. We will still be maintaining a V10 based SDK (For a while??). This can be accesed by appending `-v10` to the maven version like this:

```gradle
    implementation "com.mapspeople.mapsindoors:mapbox:4.5.0-v10"
```

For the migration of your Mapbox code implementation read the mapbox guide here: [Mapbox - Migrate to v11](https://docs.mapbox.com/android/maps/guides/migrate-to-v11/)

## Mapsindoors changes

While no breaking changes comes from the Mapsindoors. Some visual changes are introduced with Mapbox v11.

### Mapbox Standard Style

With Mapbox v11 they have introduced a new map style. That we are using in the Mapsindoors SDK. This includes new 3D visuals on the map, like extruded buildings, 3D landmarks, Trees etc. Giving a great aesthetic experience.

Insert examples.

With this, if you are using the Mapsindoors default style this will be a part of your map. We have introduced a Mapsindoors Transition level, that enable/disable the extruded buildings and mapbox POI's to not clash with Mapsindoors data. This is based on zoom level, and is by default 17. It can be configured through the `MPMapConfig`.

### 3D Models

With the Mapbox v11 release, android also finally releases support for 3D models on your map. Read more about this: //We need a guide on 3D models maybe?

### Toggling Mapsindoors features

With 4.5.0 you can now also hide specific features, allowing you to toggle between 2D and 3D. Example of disabling 3D features:

```kotlin
mapControl.hiddenFeatures = listOf(MPFeatureType.EXTRUDED_BUILDINGS,
                                             MPFeatureType.EXTRUSION_3D,
                                             MPFeatureType.WALLS_3D,
                                             MPFeatureType.MODEL_3D)
```

All features that you can enable and disable. Can be seen on `MPFeatureType`.
