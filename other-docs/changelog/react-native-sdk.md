# React Native SDK

Changelog for the MapsIndoors React Native SDK. This document structure is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/) and the project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

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
