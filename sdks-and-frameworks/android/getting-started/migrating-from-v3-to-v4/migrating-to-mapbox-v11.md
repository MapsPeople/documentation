---
description: This documentation refers to the change of the Mapbox engine in 4.5.0
---

# Migrating to Mapbox V11

## Migrating to Mapbox V11

With the release of Mapbox V11, we are migrating the V4 SDK to use Mapbox v11 to make use of the new features offered by Mapbox. As this version bump can include breaking changes for your implementation. We will still be maintaining a V10 based SDK. This will retain the maven naming as of now. Where Mapbox V11 will move to:

```gradle
implementation("com.mapspeople.mapsindoors:mapbox-v11:4.8.5")
```

For the migration of your Mapbox code implementation read the Mapbox guide here:  [Mapbox - Migrate to V11](https://docs.mapbox.com/android/maps/guides/migrate-to-v11/)

### Mapsindoors changes

While no breaking changes are introduced from the Mapsindoors SDK. Some visual changes are introduced with the use of Mapbox V11.

#### Mapbox Standard Style

With Mapbox V11 they have introduced a new map style. That is being used by the Mapsindoors SDK. This includes new 3D visuals on the map, like extruded buildings, 3D landmarks, Trees etc. Giving a great aesthetic experience.

With this, if you are using the Mapsindoors default style this will be a part of your map. We have introduced a Mapsindoors transition level, that enable/disable the extruded buildings and Mapbox POI's to not clash with the Mapsindoors data. This is based on zoom level, and is by default 17. It can be configured through the `MPMapConfig`.

#### 3D Models

With the Mapbox V11 release, android also supports rendering 3D models on your map. Read more about this:

#### Toggling Mapsindoors features

With 4.5.0 you can now also hide specific features, allowing you to toggle between 2d and 3d.

You can read about that feature here: [enabling-and-disabling-features-on-the-map.md](../../displaying-objects/enabling-and-disabling-features-on-the-map.md "mention")
