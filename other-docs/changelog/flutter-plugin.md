# Flutter Plugin

Changelog for the MapsIndoors Flutter Plugin. This document structure is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/) and the project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

### [2.1.4] 2024-02-28

**Fixed**

* Fixed issue with not being able to always get a correct route involving one way paths.

### \[2.1.3] 2024-02-13

**Changed**

* New Example app included.

**Fixed**

* Fixed another issue where `OnMapReadyListener` would not be invoked during the initial load on iOS.
* Fixed issues where `setOnMarkerClickListener`, `setOnMapClickListener` and `setOnLocationSelectedListener` would not accept being called with optional parameters.

### \[2.1.2] 2024-02-01

#### Changed

* Updated Mapsindoors SDKs
  * Android to 4.2.12
  * iOS to 4.2.14

### \[2.1.1] 2024-01-24

#### Fixed

* Fixed issue where `OnMapReadyListener` would not be invoked during the initial load on iOS.

### \[2.1.0] 2024-01-11

#### Changed

* Updated Mapsindoors SDKs
  * Android to 4.2.10
  * iOS to 4.2.13

#### Added

* Added `showRouteLegButtons` to `MPDirectionsRenderer`
* Added `setLabelOptions` to `MapsindoorsWidget`

### \[2.0.0] 2023-08-15

#### Changed

* Moved from [mapsindoors](https://pub.dev/packages/mapsindoors) to allow for multiple map providers
* Changes to classes:
  * MapControl
    * `MapControl` has merged with the `MapsIndoorsWidget`, combining them into a single entity. the `Widget` will still be built in the build tree, and accepts a listener as a parameter to wait for the MapControl part to be initialized.
  * MapsIndoors
    * Has been split up into functions on the namespace to align better with dart language standards. Some methods have changed naming to avoid collision with popular method and parameter naming (eg. `MapsIndoors.load` is now `loadMapsIndoors`)
* Changes to the Widget
  * The `MapsIndoorsWidget` has been changed to be a [`UniqueWidget`](https://api.flutter.dev/flutter/widgets/UniqueWidget-class.html), this is to ensure that the underlying MapsIndoors in the platform code can function normally.

[\
](https://docs.mapsindoors.com/changelogs/components)
