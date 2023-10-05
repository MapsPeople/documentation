# Flutter Plugin

### \[2.0.0] 2023-08-15[​](https://docs.mapsindoors.com/changelogs/flutter#200-2023-08-15) <a href="#200-2023-08-15" id="200-2023-08-15"></a>

Changelog for the MapsIndoors Flutter Plugin. This document structure is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/) and the project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

#### Changed[​](https://docs.mapsindoors.com/changelogs/flutter#changed) <a href="#changed" id="changed"></a>

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
