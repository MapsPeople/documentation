---
icon: flutter
---

# Flutter SDK

Change log for the MapsIndoors Flutter SDK. This document structure is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/) and the project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## \[4.3.0] 2025-07-22

### Added

* Added `enableMapsIndoorsDebugLogging(bool)` function to enable SDK logging to help with debugging.

### Changed

* Updated MapsIndoors SDKs:
  * iOS to 4.12.3

## \[4.2.5] 2025-07-15

### Fixed

* Resolved cocoapods dependency issue

## \[4.2.4] 2025-07-11

### Added

* Added possibility to define which map features to show when initializing the MapsIndoors widget

### Fixed

* `zoomBy` on iOS now actually zooms by the given amount from the current zoom level
* `zoom` level reported by Android when using Mapbox to not be off by 1

### Changed

* Updated MapsIndoors SDKs:
  * Android to [4.12.4](https://docs.mapsindoors.com/other/changelog/android-sdk/v4#id-4.12.4-2025-07-11)
  * iOS to [4.12.2](https://docs.mapsindoors.com/other/changelog/ios-sdk/v4#id-4.12.2-2025-07-09)

## \[4.2.3] 2025-06-24

### Fixed

* Fixed iOS compilation error

## \[4.2.2] 2025-06-20

### Fixed

* Fixed iOS not respecting `setHiddenFeatures` for 3D models

### Changed

* Updated MapsIndoors SDKs:
  * Android to [4.12.3](https://docs.mapsindoors.com/other/changelog/android-sdk/v4#id-4.12.3-2025-06-19)
  * iOS to  [4.12.1](https://docs.mapsindoors.com/other/changelog/ios-sdk/v4#id-4.12.1-2025-06-19)

## \[4.2.1] 2025-06-05

### Fixed

* Fixed Android not respecting `setHiddenFeatures` for 3D models

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.12.1

## \[4.2.0] 2025-05-14

### Added

* LabelPositioning on DisplayRules
* Elevated 2D Models (Mapbox)
  * 2D Models that are placed on Extrusions will now be raised to be placed on top
* Clip Layer (Mapbox)
  * Extruded buildings and Trees from the MapBox standard style can now be removed, when inside venue's geometry.
  * Does not remove landmarks consistently, will be solved with a future version of Mapbox
  * Requires the module to be enabled; contact MapsPeople to enable it.

### Fixed

* Added additional guards against the map being used before it is ready (Android/Google Maps)
* Fixed issue causing compass to persist even when disabled (Android)
* getLocationsByExternalIds now can return more than a single Location in the list (as would be expected) (iOS)
* Fixed issue where some routes would be incorrect if the route was calculated while offline
* Fixed issue where moving beyond the venue could cause a crash due to the floor selector
* Reenabled Mapbox Attribution as it no longer causes a crash when tapped (Android/Mapbox)

### Changed

* Updated Mapsindoors SDKs:
  * Android to 4.12.0
  * iOS to 4.11.0

## \[4.1.6] 2025-03-26

### Fixed

* Made `getLocations()` return the same result on both Android and iOS.

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.11.3
  * iOS to 4.9.6

## \[4.1.5] 2025-03-13

### Fixed

* `zoomLevelChanged` callback for custom floor selectors now gets called on iOS as well.

## \[4.1.4] 2025-03-06

### Fixed

* Removed a number of potential memory retentions in the iOS part of the Flutter plugin.
* Restored functionality of the listeners on iOS that were disabled in version 4.1.3.

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.11.2
  * iOS to 4.9.5

## \[4.1.3] 2025-02-20

### Changed

* Updated MapsIndoors SDKs:
  * iOS to 4.9.4 (to address some memory issues)

## \[4.1.2] 2025-01-30

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.10.1
  * iOS to 4.9.2

## \[4.1.1] 2025-01-09

### Fixed

* Fixed Android build issue

## \[4.1.0] 2025-01-09

### Added

* Added the ability to set a custom Mapbox style using the new `mapStyleUri` on `MapsIndoorsWidget`

### Fixed

* Fixed issue where setting `mapsIndoorsTransitionLevel` on `MapsIndoorsWidget` had no effect
* Fixed error when parsing `MPRoute` objects

### Changed

* Updated MapsIndoors SDKs:
  * iOS to 4.8.3

## \[4.0.2] 2025-01-07

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.9.1
  * iOS to 4.8.1

## \[4.0.1] 2024-12-11

**Fixed**

* Improved rendering performance of Mapbox Map view especially when pinch zooming.

### Changed

* Updated MapsIndoors SDKs:
  * iOS to 4.8.0

## \[4.0.0] 2024-11-28

Visit the [migration guide](../../sdks-and-frameworks/flutter/migration-guide.md) to see what changes are needed to upgrade from v3 to v4.

### Added

* Added `initialCameraPosition: MPCameraPosition` to the `MapWidget` constructor. If set, the initial position of the camera will be moved to the given `MPCameraPosition`.

### Changed

* Changed all uses of color `String`s to use `dart:ui` `Color` instead.
* Updated MapsIndoors SDKs:
  * Android to 4.9.0
  * iOS to 4.7.0

### Removed

* `ClearWayType` has been removed, use `ClearAvoidWayType` instead.

## \[3.1.3] 2024-10-31

### Fixed

* Fixed custom floorselectors not working properly on iOS

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.8.12
  * iOS to 4.6.2

## \[3.1.2] 2024-10-25

### Fixed

* Fixed issue where camera events would not be propagated to the Flutter layer on iOS
* Fixed behavior where iOS would throw an error when `getLocationById` could not find a location, it now returns null like on Android
* Fixed `setCollisionHandling` on `MPSolutionConfig` causing a crash
* Fixed `moveCamera`/`AnimateCamera` from a `MPCameraPosition` would not work on iOS

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.8.11 (Mapbox only)

## \[3.1.1] 2024-10-16

### Fixed

* Fixed issue where camera events would not be propagated to the Flutter layer
* Fixed issue on iOS where setting a custom floor selector
* Fixed issue on Android where `goTo(MPLocation)` did not function properly
* Fixed tilt not being applied when moving the camera using a `MPCameraUpdate` on iOS
* Fixed blank screen on Android when not using the `MapsindoorsStyle`

### Changed

* Updated MapsIndoors SDKs:
* Android to 4.8.11 (Google Maps only)
* iOS to 4.6.1

## \[3.1.0] 2024-09-11

### Added

* Added functionality for Android to update the map whenever a Display Rule is changed
* Added method on MapsIndoorsWidget to disable built-in compass

### Changed

* Disabled Mapbox Attributions button on Android as it would crash when clicked
* Updated MapsIndoors SDKs:
  * Android to 4.8.9
  * iOS to 4.5.14

## \[3.0.2] 2024-08-27

### Fixed

* Completed fix for build issue on Android devices
* Fixed issues where updating display rules would not trigger a refresh on iOS

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.8.8
  * iOS to 4.5.12

## \[3.0.1] 2024-07-08

**Fixed**

* Fixed build error when using Google Maps on Android

## \[3.0.0] 2024-06-26

### Added

* Added `setHighlight` and `clearHighlight` to `MapControlWidget` which allows you to highlight a list of locations
* Added new `MPCameraViewFitMode`: `none`, which will disable automatic camera movement when changing legs
* Added `addExcludeWayType`, `clearExcludeWayType` to `MPDirectionsService` to allow the user to exclude specific `MPHighway`s when querying for a route.
* Added two new `MPSolutionDisplayRuleEnum`s `selection` and `highlight` that allows you to modify the look of highlighted and selected Locations.
* Added support for Flat and Graphic labels, as well as 3D models
* Added new setters and getters to `MPDisplayRule`:
  * `LabelStyleGraphic`
  * `LabelType`
  * `IconScale`
  * `IconPlacement`
  * `PolygonLightnessFactor`
  * `WallLightnessFactor`
  * `ExtrusionLightnessFactor`
  * `LabelStyleTextSize`
  * `LabelStyleTextColor`
  * `LabelStyleTextOpacity`
  * `LabelStyleHaloOpacity`
  * `LabelStyleHaloWidth`
  * `LabelStyleHaloBlur`
  * `LabelStyleBearing`
  * `BadgeVisibile`
  * `BadgeZoomFrom`
  * `BadgeZoomTo`
  * `BadgeRadius`
  * `BadgeStrokeWidth`
  * `BadgeStrokeColor`
  * `BadgeFillColor`
  * `BadgePosition`
  * `Model3DModel`
  * `Model3DRotationX`
  * `Model3DRotationY`
  * `Model3DrotationZ`
  * `Model3DScale`
  * `Model3DZoomFrom`
  * `Model3DZoomTo`
  * `Model3DVisible`
* Added functionality to hide specific features from the map
  * `setHiddenFeatures` set a list of `MPFeatureType` to be hidden from the map
  * `getHiddenFeatures` get a list of currently hidden `MPFeatureType`
* Added optional venue loading, use loadMapsIndoorsWithVenues(key, venueIds) to load a specific set of venues
  * Venues can be added and removed from load at any time by using `addVenueToSync(venueId)` and `removeVenueToSync(venueId)`
  * Track the status of venues by adding a listener with `addOnVenueStatusChangedListener(MPVenueStatusListener)`
  * Get a list of synced venues with `getSyncedVenues()`
* Added functionality to disable automatic floor and building selection when moving the map
  * `setBuildingSelectionMode` set a Selection mode for Buildings on the map with `MPSelectionMode` (`automatic` or `manual`)
  * `setFloorSelectionMode` set a Selection mode for Floors on the map with `MPSelectionMode` (`automatic` or `manual`)
* Added functionality to make locations `selectable`.
  * This setting can be found on `MPLoction`, `MPPOIType` and `MPSolutionConfig`
  * Added `MPPOIType` which can be fetched from `MPSolution`
* Added `mapsIndoorsTransitionLevel` to MapsIndoorsWidget ctor
  * Sets the zoom level at which the MapsIndoors data should show, instead of extruded buildings on Mapbox Maps. Can be set to 0, if extruded buildings should not show.
* Added multi-stop navigation: It is now possible to add multiple stops to routes.
  * The existing `getRoute` method gets two optional parameters `stops` and `optimize`
  * `stops` will add the stops to the route between the `origin` and `destination`
  * `optimize` will rearrange the `stops` to make a more optimal route, but `origin` and `destination` will stay the same.

### Changed

* Updated MapsIndoors SDKs:
  * Android to 4.8.5
  * iOS to 4.5.7

### Deprecated

* Deprecated `clearWayType`: use `clearAvoidWayType` instead

## \[2.1.6] 2024-05-03

### Changed

* Updated MapsIndoors SDKs
  * iOS to 4.3.11

## \[2.1.5] 2024-03-25

### Changed

* Updated MapsIndoors SDKs
  * Android to 4.4.1
  * iOS to 4.3.8

## \[2.1.4] 2024-02-28

### Changed

* Updated MapsIndoors Android SDK 4.3.4

**Fixed**

* Fixed issue with not being able to always get a correct route involving one way paths.

## \[2.1.3] 2024-02-13

**Changed**

* New Example app included.

**Fixed**

* Fixed another issue where `OnMapReadyListener` would not be invoked during the initial load on iOS.
* Fixed issues where `setOnMarkerClickListener`, `setOnMapClickListener` and `setOnLocationSelectedListener` would not accept being called with optional parameters.

## \[2.1.2] 2024-02-01

### Changed

* Updated MapsIndoors SDKs
  * Android to 4.2.12
  * iOS to 4.2.14

## \[2.1.1] 2024-01-24

### Fixed

* Fixed issue where `OnMapReadyListener` would not be invoked during the initial load on iOS.

## \[2.1.0] 2024-01-11

### Changed

* Updated MapsIndoors SDKs
  * Android to 4.2.10
  * iOS to 4.2.13

### Added

* Added `showRouteLegButtons` to `MPDirectionsRenderer`
* Added `setLabelOptions` to `MapsindoorsWidget`

## \[2.0.0] 2023-08-15

### Changed

* Moved from [mapsindoors](https://pub.dev/packages/mapsindoors) to allow for multiple map providers
* Changes to classes:
  * MapControl
    * `MapControl` has merged with the `MapsIndoorsWidget`, combining them into a single entity. the `Widget` will still be built in the build tree, and accepts a listener as a parameter to wait for the MapControl part to be initialized.
  * MapsIndoors
    * Has been split up into functions on the namespace to align better with dart language standards. Some methods have changed naming to avoid collision with popular method and parameter naming (eg. `MapsIndoors.load` is now `loadMapsIndoors`)
* Changes to the Widget
  * The `MapsIndoorsWidget` has been changed to be a [`UniqueWidget`](https://api.flutter.dev/flutter/widgets/UniqueWidget-class.html), this is to ensure that the underlying MapsIndoors in the platform code can function normally.
