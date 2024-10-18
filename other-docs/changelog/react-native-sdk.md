# React Native SDK

Changelog for the MapsIndoors React Native SDK. This document structure is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/) and the project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

### \[2.1.1] 2024-10-17

#### Changed

* Specified Google Maps linkage on iOS. If upgrading from earlier versions make sure to remove the `post_install` script inside the podfile that removes the static linked library from `MapsIndoorsGoogleMaps`

#### Updated

* Updated MapsIndoors iOS SDK to 4.6.1
* Updated MapsIndoors Android SDK to 4.8.11

### \[2.1.0] 2024-09-27

#### Added

* `camera:MPCameraPosition`  to the `MapView` view. To set an initial camera position when showing the map.
* `showCompass:boolean` to the `MapView` view. To enable/disable if the compass should show when rotating the map

#### Fixed

* Fixed a compile issue with xcode 16

#### Updated

* Updated to Mapbox 11.7.0 on Android

### \[2.0.7] 2024-09-12

#### Fixed

* Fixed a potential crash happening when hot reloads happen to the Mapview
* Fixed an issue where selectable property was nul on locations
* Fixed an issue where the map would not render before a map interaction
* Fixed an issue where requesting a route would never resolve/reject the promise
* Fixed an issue where the route would reappear after clearing it on the DirectionsRenderer

#### Updated

* Updated iOS SDK to 4.5.15
* Updated Android SDK to 4.8.9

### \[2.0.6] 2024-08-30

#### Updated

#### Fixed

* Fixed missing events being sent when using MPFloorSelectorInterface on iOS

#### Updated

* Updated iOS SDK to 4.5.13

### \[2.0.5] 2024-08-21

#### Added

* Added optional legIndex, to set the initial leg index of a route. When using `setRoute` on `MPDirectionsRenderer`

#### Fixed

* Fixed issue with directions not being rendered on subsequent map renderings
* Fixed issue with imageUrl being undefined on `MPLocation`
* Fixed issue where camera events was not being sent on iOS
* Fixed issue where tilt was not used when set on Camera updates
* Fixed flickering when selecting locations on iOS

#### Updated

* Updated Android SDK to 4.8.8
* Updated iOS SDK to 4.5.12

### \[2.0.4] 2024-08-01

#### Updated

#### Fixed

* Issue with subsequent Mapcontrol creation on iOS, causing rendering errors

#### Updated

* Updated iOS SDK to 4.5.11

### \[2.0.3] 2024-07-30

#### Added

* showRoadLabels to MPMapConfig
  * Sets wether the Mapbox Road labels should be shown on the map. If left undefined, it follows the transition level.

#### Fixed

* Issue with where map data is not rendered on the map, while MapControl has loaded successfully on iOS
* Issue with DisplayRule changes not updating before a map interaction

#### Updated

* Updated Android SDK to 4.8.7
* Updated iOS SDK to 4.5.10

### \[2.0.2] 2024-06-25

#### Updated

* Updated iOS SDK to 4.5.6

### \[2.0.1] 2024-06-20

#### Updated

* Updated iOS SDK to 4.5.6

#### Fixed

* Fixed issue where tiles would fade away, regardless of `mapsindoorsTransitionLevel` on Mapbox iOS

### \[2.0.0] 2024-06-19

#### Added

* Added new `MPCameraViewFitMode.none`to disable camera movement, when changing legs on `MPDirectionsRenderer`
* Added new properties to `MPDisplayRule`:
  * `setLabelStyleGraphic` sets a graphic label:
    * `backgroundImage`
    * `stretchX`
    * `stretchY`
    * `content`
  * `getLabelStyleGraphic`
  * `set/getModel3DModel`
  * `set/getModel3DRotationX`
  * `set/getModel3DRotationY`
  * `set/getModel3DrotationZ`
  * `set/getModel3DScale`
  * `set/getModel3DZoomFrom`
  * `set/getModel3DZoomTo`
  * `set/isModel3DVisible`
* Added new methods on `MapControl`:
  * `setHiddenFeatures` set a list of `MPFeatureType` to be hidden from the map
  * `getHiddenFeatures` get a list of currently hidden `MPFeatureType`
  * `setBuildingSelectionMode` set a Selection mode for Buildings on the Map with `MPSelectionMode` (automatic or manual)
  * `setFloorSelectionMode` set a Selection mode for Floors on the Map with `MPSelectionMode` (automatic or manual)
  * `getBuildingSelectionMode` get the current selection mode on `MapControl`
  * `getFloorSelectionMode` get the current selection mode on `MapControl`
* Added `types: MPPOIType[]` on `MPSolution` to get a list of types for the solution
* Added `setSelectable` and `isSelectable` on `MPLocation`, `MPPOIType` and `MPSolutionConfig`
* Added `mapsIndoorsTransitionLevel?: number` to `MPMapConfig`
  * Sets the zoom level at which the MapsIndoors data should show, instead of extruded buildings on Mapbox Maps. Can be set to 0, if extruded buildings should not show.
* Added `showMapMarkers?: boolean` to `MPMapConfig`
  * Sets wether the Mapbox POI and Places markers hould be shown on the map. If left undefined, it follows the transition level.

#### Updated

* Updated iOS SDK to 4.5.4
* Updated Android SDK to 4.8.5

### \[1.3.2] 2024-06-07

#### Updated

* Updated iOS SDK to 4.5.1
* Updated Android SDK to 4.8.4

#### Fixed

* Fixed issue where route would not be optimised on iOS when querying multi stop routes
* Fixed issue where the first leg would not be animated on iOS

### \[1.3.1] 2024-05-31

#### Updated

* Updated iOS SDK to 4.4.1
* Updated Android SDK to 4.8.3

### \[1.3.0] 2024-05-27

#### Added

* Added Support for Mutli-stop navigation
  * Added optional `stops: MPPoint[]` and `optimize: boolean` to `MPDirectionsService.getRoute`
  * Added `setDefaultRouteStopIcon` to `MPDirectionsRenderer`
  * Added optional `stopIcons: Map<number, RouteStopIconConfig>`  to `MPDirectionsRenderer.setRoute`
  * Added `MPRouteStopIconConfig` for changing the look of the default stop icons
  * Added `ordered_stop_indexes` to `MPRoute`
  * Added `legStartReason`, `legEndReason` and `stopIndex` to `MPRouteLeg`

#### Updated

* Updated iOS SDK to 4.4.0
* Updated Android SDK to 4.8.1
* Updated Android Mapbox SDK to 10.17.1

#### Fixed

* Fixed zoom not being applied when changing camera with a `MPCameraPosition` on Mapbox iOS

### \[1.2.1] 2024-05-03

#### Changed

* Upped the minimum version requirement for iOS to 14.

#### Updated

* Updated iOS SDK to 4.3.9
* Updated Android SDK to 4.6.0

#### Fixed

* Fixed an issue with the privacy manifest not allowing release of apps on app store

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixes from updates to native SDKs

### \[1.2.0] 2024-04-29

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated iOS SDK to 4.3.9
* Updated Android SDK to 4.6.0

#### Added[​](https://docs.mapsindoors.com/changelogs/react-native#added) <a href="#added" id="added"></a>

* New optional list of strings, with venue id's on `load`. For optional venue loading.
* New methods to support optional venue loading.
  * `addVenuesToSync(venues: string[])`
  * `removeVenuesToSync(venues: string[])`
  * `getSyncedVenues(): Promise<string[]>`
* The MapsIndoors iOS SDK now includes a Privacy Manifest as described by Apple in Upcoming third-party SDK requirements. This also includes an update to the Mapbox 10.17.0 that includes a fix to the privacy manifest of Mapbox.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixes from updates to native SDKs

### \[1.2.0] 2024-04-29

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated iOS SDK to 4.3.9
* Updated Android SDK to 4.6.0

#### Added[​](https://docs.mapsindoors.com/changelogs/react-native#added) <a href="#added" id="added"></a>

* New optional list of strings, with venue id's on `load`. For optional venue loading.
* New methods to support optional venue loading.
  * `addVenuesToSync(venues: string[])`
  * `removeVenuesToSync(venues: string[])`
  * `getSyncedVenues(): Promise<string[]>`
* The MapsIndoors iOS SDK now includes a Privacy Manifest as described by Apple in Upcoming third-party SDK requirements. This also includes an update to the Mapbox 10.17.0 that includes a fix to the privacy manifest of Mapbox.

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixes from updates to native SDKs

### \[1.1.0] 2024-02-15

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated iOS SDK to 4.3.2
* Updated Android SDK to 4.3.1
* New default rendering of selection. Can be reverted by changing `isNewSelection` to `false`

#### Added[​](https://docs.mapsindoors.com/changelogs/react-native#added) <a href="#added" id="added"></a>

* Exclude highway support
* Select/highlight support with new DisplayRule settings
  * iconScale
  * iconPlacement
  * labelType
  * polygonLightnessFactor
  * wallLightnessFactor
  * extrusionLightnessFactor
  * labelStyleTextSize
  * labelStyleTextColor
  * labelStyleTextOpacity
  * labelStyleHaloOpacity
  * labelStyleHaloWidth
  * labelStyleHaloBlur
  * labelStyleBearing
  * badgeVisible
  * badgeZoomFrom
  * badgeZoomTo
  * badgeRadius
  * badgeStrokeWidth
  * badgeStrokeColor
  * badgeFillColor
  * badgePosition
* Support for non-selectable locations
* Support for flat labels

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Issue where compass would not show up on Mapbox for iOS
* Fixes from updates to native SDKs

### \[1.0.9] 2024-01-04

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Issue where subsequent maps would not be able to draw a route on iOS

### \[1.0.8] 2023-12-19[​](https://docs.mapsindoors.com/changelogs/react-native#103-2023-12-19) <a href="#106-2023-12-19" id="106-2023-12-19"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated iOS SDK to 4.2.13
* Updated Android SDK to 4.2.8

### \[1.0.7] 2023-12-08[​](https://docs.mapsindoors.com/changelogs/react-native#103-2023-12-08) <a href="#106-2023-12-08" id="106-2023-12-08"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated iOS SDK to 4.2.12

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Improved camera padding behavior

### \[1.0.6] 2023-11-24[​](https://docs.mapsindoors.com/changelogs/react-native#105-2023-11-24) <a href="#105-2023-11-24" id="105-2023-11-24"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated Android SDK to 4.2.6
* Updated iOS SDK to 4.2.10

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixed case where tiles would not show up after loading the map on iOS
* Fixed an issue with route legs missing geometries for steps on iOS

### \[1.0.4] 2023-10-08[​](https://docs.mapsindoors.com/changelogs/react-native#104-2023-10-08) <a href="#104-2023-10-08" id="104-2023-10-08"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated Android SDK to 4.2.3
* Updated iOS SDK to 4.2.6
* Changed setLabelOptions to have optional parameters

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixed issue with showRouteLegButtons not working on iOS

### \[1.0.3] 2023-09-25[​](https://docs.mapsindoors.com/changelogs/react-native#103-2023-09-25) <a href="#103-2023-09-25" id="103-2023-09-25"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed) <a href="#changed" id="changed"></a>

* Updated Android SDK to 4.2.2
* Updated iOS SDK to 4.2.5

#### Added[​](https://docs.mapsindoors.com/changelogs/react-native#added) <a href="#added" id="added"></a>

* Added support for hiding route leg buttons
* Added support for setting label textsize, color and halo

### \[1.0.2] 2023-09-04[​](https://docs.mapsindoors.com/changelogs/react-native#102-2023-09-04) <a href="#102-2023-09-04" id="102-2023-09-04"></a>

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed-1) <a href="#changed-1" id="changed-1"></a>

* Updated Android SDK to 4.1.11
* Updated iOS SDK to 4.2.4

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed) <a href="#fixed" id="fixed"></a>

* Fixed issue with abutters on MPRouteStep missing on iOS
* Fixed issue with highways on MPRouteStep missing on iOS
* Fixed issue with HTML instructions and Manoeuvre contradicting each other on MPRoutestep

### \[1.0.1][​](https://docs.mapsindoors.com/changelogs/react-native#101) <a href="#101" id="101"></a>

#### Added[​](https://docs.mapsindoors.com/changelogs/react-native#added-1) <a href="#added-1" id="added-1"></a>

* iOS now has working cameraEvents

#### Changed[​](https://docs.mapsindoors.com/changelogs/react-native#changed-2) <a href="#changed-2" id="changed-2"></a>

* Updated Android SDK to 4.1.10
* Updated iOS SDK to 4.2.2

#### Fixed[​](https://docs.mapsindoors.com/changelogs/react-native#fixed-1) <a href="#fixed-1" id="fixed-1"></a>

* Fixed Crash when switching between legs of a route on iOS
* Fixed Crash when calling animateCamera on iOS
* Fixed Parsing issues on some geometries from native code

### \[1.0.0][​](https://docs.mapsindoors.com/changelogs/react-native#100) <a href="#100" id="100"></a>

* Full release, you can find the packages available on nmpjs: [google maps](https://www.npmjs.com/package/@mapsindoors/react-native-maps-indoors-google-maps), [mapbox](https://www.npmjs.com/package/@mapsindoors/react-native-maps-indoors-mapbox)
